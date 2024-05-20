<template>
  <div class="col fondo"> 
    <h1 class="subtitulo">Filter</h1>
    <div class="count">
      Results: {{ this.countValue }} 
    </div>
    <div class="filtro">
      <h2 >Gender</h2>
      <span v-for="option in genderOptions" :key="option.id" >
        <label>
          <input type="checkbox" :value="option.value" v-model="filtrosGender" v-on:change="cambiaFiltro">
          {{ option.label }}
        </label><br>
      </span>
    </div>
    <div class="filtro">
      <h2 >Status</h2>
      <span v-for="option in statusOptions" :key="option.id" >
        <label>
          <input type="checkbox" :value="option.value" v-model="filtrosStatus" v-on:change="cambiaFiltro">
          {{ option.label }}
        </label><br>
      </span>
    </div>
    <div class="filtro">
      <h2 >Species</h2>
      <span v-for="option in speciesOptions" :key="option.id" >
        <label>
          <input type="checkbox" :value="option.value" v-model="filtrosSpecies" v-on:change="cambiaFiltro">
          {{ option.label }}
        </label><br>
      </span>
    </div>
    <!-- <b-button class="botonRosa" @click="filtrarConsulta">Filter</b-button> -->
    <b-button class="botonRosa" >Filter</b-button>
  </div>
</template>

<script>
export default {
  name: 'Filtro',
  props: {
    countValue: Number
  },
  data() {
    return {
      filtrosGender: [],
      filtrosStatus: [],
      filtrosSpecies: [],
      cadenaFiltros: " ",
      copyCadenaFiltros: " ",
      genderOptions: [
        { id: 1, value: 'female', label: 'Female' },
        { id: 2, value: 'male', label: 'Male' },
        { id: 3, value: 'genderless', label: 'Genderless' },
        { id: 4, value: 'unknown', label: 'Unknown' }
      ],
      statusOptions: [
        { id: 1, value: 'alive', label: 'Alive' },
        { id: 2, value: 'dead', label: 'Dead' },
        { id: 3, value: 'unknown', label: 'Unknown' }
      ],
      speciesOptions: [
        { id: 1, value: 'human', label: 'Human' },
        { id: 2, value: 'Alien', label: 'Alien' },
        { id: 3, value: 'Animal', label: 'Animal' },
        { id: 4, value: 'Humanoid', label: 'Humanoid' },
        { id: 5, value: 'Poopybutthole', label: 'Poopybutthole' },
        { id: 6, value: 'Robot', label: 'Robot' },
        { id: 7, value: 'Cronenberg', label: 'Cronenberg' },
        { id: 8, value: 'Disease', label: 'Disease' },
        { id: 9, value: 'Mythological Creature', label: 'Mythological Creature' },
        { id: 10, value: 'unknown', label: 'Unknown' }
      ]
    };
  },
  methods: {
    imprimeFiltros(){
      console.log("FILTROS");
      console.log("Genero: "+this.filtrosGender);
      console.log("Species: "+this.filtrosSpecies);
      console.log("Status: "+this.filtrosStatus);
    },
    cambiaFiltro(){
      this.cadenaFiltros = " ";
      if(this.filtrosGender.length === 0 && this.filtrosSpecies.length === 0 && this.filtrosStatus.length === 0 && this.rows >= 826) {
        console.log("Cadena sin filtros");
      }
      else {
        if(this.filtrosGender.length !== 0){
          this.cadenaFiltros = this.cadenaFiltros + 'gender: "' + this.filtrosGender[0] + '"';
        }
        if(this.filtrosSpecies.length !== 0){
          if(this.cadenaFiltros !== " "){
            this.cadenaFiltros = this.cadenaFiltros + ', ';
          }
          this.cadenaFiltros = this.cadenaFiltros + 'species: "' + this.filtrosSpecies[0] + '"';
        }
        if(this.filtrosStatus.length !== 0){
          if(this.cadenaFiltros !== " "){
            this.cadenaFiltros = this.cadenaFiltros + ', ';
          }
          this.cadenaFiltros = this.cadenaFiltros + 'status: "' + this.filtrosStatus[0] + '"';
        }
        if(this.cadenaFiltros !== " "){
          this.cadenaFiltros = `filter: { ${this.cadenaFiltros} }`;
        }
        this.copyCadenaFiltros = this.cadenaFiltros;
        // this.imprimeFiltros(); //Para imprimir los arregloss en tiempo real
        // console.log("Cadena con filtros: "+this.cadenaFiltros);
        this.emiteFiltros();
        // console.log(typeof this.cadenaFiltros);
      }
      // this.scrollToTop();
    },
    emiteFiltros(){
			this.$emit('cambia-filtro', this.cadenaFiltros);
			// console.log("Los filtros emitidos: "+this.cadenaFiltros);
    }
  }
}
</script>


<style>

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
  background : #DC143C !important;
  border: solid 1px #fdb4bf !important;
  box-shadow: 1px 1px 2px #8B0000;
  /* transition: all 0.2s ease; */
}

.subtitulo {
  color: rgb(251, 149, 166);
  font-weight: bold;
  font-size: 25px;
  background: rgb(250, 250, 250);
}

.fondo {
  background: rgb(250, 250, 250);
  padding: 10px;
  margin-left: 20px;
  margin-bottom: 20px;
  border-radius: 10px;
  text-align: center;
}

#buscar {
  width: 300px;
  height: 40px;
  margin-right: 5px;
  border-radius: 8px;
  vertical-align: middle;
  padding: 10px;
  border: solid 2px gray;
}

.filtro {
  background: #FFC0CB;
  text-align: left;
  padding: 8px;
  border-radius: 8px;
  margin-bottom: 10px;
}

.filtro h2 {
  font-size: 15px;
  font-weight: bold;
  margin: 3px;
}

.filtro input {
  font-size: 3px;
  vertical-align: middle; 
  margin: 5px;
}

.filtro label {
  font-size: 14px;
  vertical-align: middle; 
  display: inline;
}

</style>