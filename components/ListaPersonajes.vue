<template>
	<div>
		<div class="rowsCont">
			<label for="sortSelect">Ordenar por:      </label>
      <b-form-select id="sortSelect" v-model="sort" :options="optionsSort" ></b-form-select>
			<label for="rows-input">Número de registros:      </label>
			<b-form-input id="rows-input" v-model="perPage" type="number" value="20" min="1" max="30"></b-form-input>
		</div>
		<b-table id="listaPokemon" striped hover bordered light :fields="columnas" :items="datos" :current-page="currentPage" @input="emiteNewCount" :per-page="perPage">
			<template #cell(imagenPersonaje)="data">
				<b-img v-if="data.item.pokemon_v2_pokemonsprites_aggregate.nodes[0].sprites!=null" :src="`${data.item.pokemon_v2_pokemonsprites_aggregate.nodes[0].sprites}`" />
				<b-img v-else :src="'https://media.istockphoto.com/id/1055079680/vector/black-linear-photo-camera-like-no-image-available.jpg?s=612x612&w=0&k=20&c=P1DebpeMIAtXj_ZbVsKVvg-duuL0v9DlrOZUvPG6UJk='"/>
			</template>
			<template #cell(generacionP)="data">
				{{ `${data.item.pokemon_v2_pokemonspecy.pokemon_v2_generation.name}` }}
			</template>
			<template #cell(colorP)="data">
				{{ `${data.item.pokemon_v2_pokemonspecy.pokemon_v2_pokemoncolor.name}` }}
			</template>
			<template #cell(height)="data">
				{{ `${data.item.height/10}` }} m
			</template>
			<template #cell(weight)="data">
				{{ `${data.item.weight/10}` }} kg
			</template>
			<template #cell(pokemon_v2_pokemontypes_aggregate)="data">
				<ul class="tiposPokemon" v-for="(type, id) in data.item.pokemon_v2_pokemontypes_aggregate.nodes" :key="id">
					<li > {{ type.pokemon_v2_type.name }} </li>
				</ul>
			</template>
			<template #cell(boton)="data">
				<b-button class="botonRosa" @click="send(data.item.id)">Info</b-button>
			</template>
		</b-table>
		<div>
			<b-pagination v-model="currentPage" :current-page="currentPage" pills size="lg" align="center" :total-rows="rows" :per-page="perPage" limit="10" aria-controls="listaPokemon"></b-pagination>
		</div>
	</div>
</template>

<script>
export default {
	name: 'ListaPersonajes',
	props: {
		filterString: String,
		searchString: String
	},
	data() {
		return {
			datos: [],
			rows: 20,
      currentPage: 1,
			perPage: 20,
      query: null,
      sort: 1,
			count: null,
			copyFiltro: " ",
			columnas: [
        {
          label: "ID",
          key: "id"
        },
        {
          label: "Pokemon",
          key: "imagenPersonaje"
        },
        {
          label: "Nombre",
          key: "name"
        },
        {
          label: "Generación",
          key: "generacionP"
        },
        {
          label: "Color",
          key: "colorP"
        },
        {
          label: "Altura",
          key: "height"
        },
        {
          label: "Peso",
          key: "weight"
        },
        {
          label: "Tipo",
          key: "pokemon_v2_pokemontypes_aggregate"
        },
        {
          label: "See more",
          key: "boton"
        },
      ],
      optionsSort: [
        { value: 1, text: 'A - Z' },
        { value: 2, text: 'Z - A' },
        { value: 3, text: 'ID ascendente' },
        { value: 4, text: 'ID descendente' }
      ]
		}
	},
	mounted() {
		this.checkPage();
		this.getData();
	},
	computed: {
		getTableData(){
			console.log("EJECUTANDO COMPUTED: "+this.currentPage);
			this.scrollToTop();
      // localStorage.setItem('currentPage', this.currentPage);
      // console.log("La localStorage es en COMPUTED: "+localStorage.getItem('currentPage'));
			console.log("los filtros actuales son: "+this.filterString);
			return () => this.getData();
		},
		actualiza(){
			// console.log("EJECUTANDO COMPUTED2: "+this.copyFilter);
			this.scrollToTop();
			return () => this.getTableData();
		}
	},
	methods: {
		async getData() {
			var queryVar1 = `
				query Pokemon_v2_pokemon {
					pokemon_v2_pokemon  {
							height
							id
							name
							weight
							pokemon_v2_pokemonsprites_aggregate {
									nodes {
											sprites(path: "other.official-artwork.front_default")
									}
							}
							pokemon_v2_pokemontypes_aggregate {
									nodes {
											pokemon_v2_type {
													name
											}
									}
							}
					}
					pokemon_v2_pokemon_aggregate {
							aggregate {
									count
							}
					}
			}
			`;
      var queryVar2 = `
          query Pokemon_v2_pokemon {
          pokemon_v2_pokemon {
            height
            id
            name
            weight
            pokemon_v2_pokemontypes_aggregate {
              nodes {
                pokemon_v2_type {
                  name
                }
              }
            }
            pokemon_v2_pokemonstats {
              base_stat
              pokemon_v2_stat {
                name
              }
            }
            pokemon_v2_pokemonabilities {
              pokemon_v2_ability {
                name
              }
            }
            pokemon_v2_pokemonsprites_aggregate {
              nodes {
                sprites(path: "other.official-artwork.front_default")
              }
            }
            pokemon_v2_pokemonspecy {
              pokemon_v2_evolutionchain {
                pokemon_v2_pokemonspecies {
                  name
                  id
                }
              }
              pokemon_v2_pokemoncolor {
                name
                id
              }
              generation_id
              pokemon_v2_generation {
                name
                id
              }
            }
          }
          pokemon_v2_pokemon_aggregate {
            aggregate {
              count
            }
          }
        }
      `;
			var queryVar3 = `
          query Pokemon_v2_pokemon {
            pokemon_v2_pokemon(order_by: {name: asc}) {
              height
              id
              name
              weight
              pokemon_v2_pokemontypes_aggregate {
                nodes {
                  pokemon_v2_type {
                    name
                  }
                }
              }
              pokemon_v2_pokemonstats {
                base_stat
                pokemon_v2_stat {
                  name
                }
              }
              pokemon_v2_pokemonabilities {
                pokemon_v2_ability {
                  name
                }
              }
              pokemon_v2_pokemonsprites_aggregate {
                nodes {
                  sprites(path: "other.official-artwork.front_default")
                }
              }
              pokemon_v2_pokemonspecy {
                pokemon_v2_evolutionchain {
                  pokemon_v2_pokemonspecies {
                    name
                    id
                  }
                }
                pokemon_v2_pokemoncolor {
                  name
                  id
                }
                generation_id
                pokemon_v2_generation {
                  name
                  id
                }
              }
            }
            pokemon_v2_pokemon_aggregate {
              aggregate {
                count
              }
            }
          }
        `;
      // console.log(queryVar);
			const response = await this.$axios.$post('https://beta.pokeapi.co/graphql/v1beta', { query: this.query });
			this.datos = response.data.pokemon_v2_pokemon;
      // console.log(response.data.pokemon_v2_pokemon);
			this.rows = response.data.pokemon_v2_pokemon_aggregate.aggregate.count;
			return response.data.pokemon_v2_pokemon
		},
		emiteNewCount() {
			this.$emit('cambia-count', this.rows);
			// console.log("El valor emitido: "+this.rows);
		},
		scrollToTop() {
      // Hacer scroll suave al principio de la página
      if (process.client) {
        window.scrollTo({
          top: 0,
          behavior: 'smooth'
        });
      }
		},
		send(idValue) {
			this.$router.push({ path: 'about', query: { id: idValue } });
		},
		checkPage(){
      var pageLS = localStorage.getItem('currentPage');
			if(pageLS === null){
				pageLS = 1;
			}
      this.currentPage = pageLS;
      this.sort=1;
      console.log("La query debería ser: "+this.query);

      
      var perPageLS = localStorage.getItem('perPage');
			if(perPageLS === null){
				perPageLS = 20;
			}
      this.perPage = perPageLS;
      

      // console.log("La página en check: "+pageLS);

			if(this.$route.params.filtros){
				
				this.copyFiltro = this.$route.params.filtros;
			}
		}
	},
	watch: {
		filterString: function() {
			// console.log("CAMBIA FILTER STRING: "+ this.filterString);
			this.copyFiltro = this.filterString;
			// this.currentPage = 0;
			// this.currentPage = 1;
			// this.copyFilter = this.filterString;
			// console.log("COPY STRING: "+ this.copyFilter);
			this.getTableData();
		}
    ,
    currentPage: function() {
      console.log("La currP: "+this.currentPage);
      localStorage.setItem('currentPage', this.currentPage);
      console.log("La localStorage nueva: "+localStorage.getItem('currentPage'));
    },
    perPage: function(){
      localStorage.setItem('perPage', this.perPage);
      console.log("La PP localStorage nueva: "+localStorage.getItem('perPage'));
    },
    sort: function () {
      var sortString;
      console.log("El valor de sort es: "+this.sort);
      switch(this.sort){
        case 1: 
          sortString = '{name: asc}';
          break;
        case 2: 
          sortString = '{name: desc}';
          break;
        case 3: 
          sortString = '{id: asc}';
          break;
        case 4: 
          sortString = '{id: desc}';
          break;
        default:
          sortString = '{name: asc}';
          console.log("Switch no funciona");
      }
      this.query = `
        query Pokemon_v2_pokemon {
          pokemon_v2_pokemon(order_by: ${sortString}) {
            height
            id
            name
            weight
            pokemon_v2_pokemontypes_aggregate {
              nodes {
                pokemon_v2_type {
                  name
                }
              }
            }
            pokemon_v2_pokemonsprites_aggregate {
              nodes {
                sprites(path: "other.official-artwork.front_default")
              }
            }
            pokemon_v2_pokemonspecy {
              pokemon_v2_pokemoncolor {
                name
                id
              }
              generation_id
              pokemon_v2_generation {
                name
                id
              }
            }
          }
          pokemon_v2_pokemon_aggregate {
            aggregate {
              count
            }
          }
        }
      `;
      console.log("QUERY: "+this.query);
      getTableData();
    }
  }
		// ,
		// searchString: function() {
		// 	console.log("CAMBIA SEARCH STRING: "+ this.searchString);
		// 	// this.currentPage = 0;
		// 	// this.currentPage = 1;
		// 	// this.copyFilter = this.filterString;
		// 	// console.log("COPY STRING: "+ this.copyFilter);
		// 	// this.getTableData();

		// 	// Código para buscar con searchString
		// }
	}
}
</script>


<style>
img {
  width: 120px; 
  height: auto; 
}

ul {
  list-style-type: none;
	display: flex;
	justify-content: center;
	vertical-align: middle;
	padding: 0%;
}

.tiposPokemon li {
	background-color: #fdb4bf7c;
	padding: 7px;
	padding-bottom: 10px;
	align-content: center;
	justify-content: center;
	vertical-align: middle;
	width: 70%;
	border-radius: 15px;
}

.b-table tr {
  text-align: center; /* Centrado horizontal */
  vertical-align: middle; /* Centrado vertical */
}

.titulo {
  color: rgb(251, 149, 166);
  font-weight: bold;
  padding: 10px;
  margin: 20px;
  background: rgb(250, 250, 250);
}

.count {
  background-color: rgb(224, 224, 224);
  padding: 10px;
  margin: 20px;
  font-weight: bold;
}

.botonRosa {
  background : #FA8072 !important;
  border: solid 1px #fdb4bf !important;
  height: 40px;
}

.botonRosa:hover {
  background : #FA8072 !important;
  border: solid 1px #fdb4bf !important;
}

.b-pagination button { /* estilos para todos los botones */
  background-color: #f8f9fa; /* Color de fondo de los botones */
  border-color: #fdb4bf; /* Color del borde del botón activo */
  color: #e57d90; /* Color del texto de los botones */
}

.b-pagination button:hover {
  color: #ffffff; 
  border-color: #fdb4bf; 
  background-color: #fdb4bf; /* Cambiar color de fondo en hover */
  font-weight: bold;
}

.page-item.active .page-link { /* el boton de la página actual */
    color: #fff;
    background-color: #FA8072;
    border-color: #e57d90;
    font-weight: bold;
}

.page-link.active, .active > .page-link {
    border-color: #ed3591;
    background-color: #ed3591; /* Color de fondo del botón activo */
    color: white;
}

.rowsCont {
	background-color: #87CEFA;
	padding: 15px;
	display: flex;
  justify-content: right; 
	align-items: center;
}

.rowsCont input {
	width: 150px;
	vertical-align: middle;
	margin-left: 20px;
}

.rowsCont select {
	width: 150px;
  height: 38px;
	vertical-align: middle;
	margin-left: 20px;
  margin-right: 20px;
  border-radius: 8px;
  border: none;
  padding: 7px;
  font-size: 15px;
}

.rowsCont label {
	vertical-align: middle;
	color: whitesmoke;
	font-weight: bold;
	font-size: 20px;
}

</style>