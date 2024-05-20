<template>
    <div>
        HOLAAA PRUEBA
        <div class="contieneBoton">
            <BButton size="lg" class="botonRosa2" @click="regresar()" >Characters List</BButton>
        </div>

    </div>
</template>

<script>
export default {
    methods: {
        regresar() {
            this.$router.push({ name: 'index'});
        }
    }
}
</script>