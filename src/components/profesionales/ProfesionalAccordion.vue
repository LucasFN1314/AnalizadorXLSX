<template>
    <div class="accordion-item">

        <h2 class="accordion-header" id="headingOne">
            <button class="accordion-button collapsed" type="button" data-bs-toggle="collapse"
                :data-bs-target="'#' + index" aria-expanded="true" aria-controls="collapseOne">
                {{ servicio.nombre }} - {{getRealizadas(servicio)}} Realizadas
            </button>
        </h2>

        <div :id="'' + index" class="accordion-collapse collapse">
            <div class="accordion-body">

                <table class="table">
                    <thead>
                        <tr>
                            <th scope="col">Profesional</th>
                            <th scope="col">Realizadas</th>
                            <th scope="col">Porcentaje</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="(profesional, key) in servicio.profesionales" :key="key">
                            <td v-if="profesional.realizadas >0">{{profesional.nombre}}</td>
                            <td v-if="profesional.realizadas >0">{{profesional.realizadas }}</td>
                            <td v-if="profesional.realizadas >0">{{ getPorcentaje(profesional.realizadas, getRealizadas(servicio)) }}%</td>
                        </tr>
                    </tbody>
                </table>

            </div>
        </div>

    </div>
</template>

<script>
export default {
    props: [
        'departamento',
        'servicio',
        'index'
    ],
    methods: {
        getPorcentaje(x1, x2) {
            // x1 = prof.real, x2 = dpto.real
            return ((x1*100)/x2).toFixed(2);
        },
        getRealizadas(servicio) {
            let realizadas = 0;
            servicio.profesionales.map((profesional) => {
                realizadas+=profesional.realizadas;
            })
            return realizadas;
        }
    }
}
</script>

<style scoped>
.accordion-body {
}
</style>