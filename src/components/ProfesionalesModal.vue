<template>
    <div class="modal-prof-container" v-if="show_modal">

        <div class="modal-prof">
            <div class="close_modal">
                <i class="bi bi-x-square-fill" v-on:click="close()"></i>
            </div>
            <ul class="accordion">
                <h1 class="title">{{ departamento.nombre }}</h1>

                <li v-for="(servicio, index) in departamento.servicios" :key="index">
                    <ProfesionalAccordion :departamento="departamento" :servicio="servicio" :index="index" v-if="servicio.realizadas >0"></ProfesionalAccordion>
                    
                    <!--<div class="accordion-item">
                        
                        <h2 class="accordion-header" id="headingOne">
                            <button class="accordion-button collapsed" type="button" data-bs-toggle="collapse"
                                :data-bs-target="'#' + index" aria-expanded="true" aria-controls="collapseOne">
                                {{ servicio.nombre }}
                            </button>
                        </h2>

                        <div :id="'' + index" class="accordion-collapse collapse">
                            <div class="accordion-body">
                                A
                            </div>
                        </div>

                    </div>-->
                </li>
            </ul>
        </div>
    </div>
</template>

<script>
import ProfesionalAccordion from './profesionales/ProfesionalAccordion.vue'
export default {
    data() {
        return {
            show_modal: false,
            departamento: '',
            body: null,

        }
    },
    mounted() {
        this.body = document.getElementsByTagName('body')[0];
    },
    methods: {
        show(departamento) {
            this.show_modal = true;
            this.departamento = departamento;
            this.body.setAttribute('style', 'overflow:hidden;');
            window.scrollTo(0, 0);
        },
        close() {
            this.body.setAttribute('style', 'overflow-y:scroll;');
            this.show_modal = false;
        }
    },
    components: {
        ProfesionalAccordion
    }
};
</script>

<style scoped>
.modal-prof-container {
    position: absolute;
    top: 0;
    left: 0;

    width: 100vw;
    height: 100vh;

    z-index: 100;

    display: flex;
    flex-direction: column;

    align-items: center;
    justify-content: center;

    background-color: rgba(0, 0, 0, 0.185);

}

.modal-prof {
    background-color: rgb(255, 255, 255);
    width: 80vw;
    height: 80vh;

    position: relative;
    padding: 2em;
}

.close_modal {
    position: absolute;
    top: 0;
    right: 0;
    margin: 2em;
    margin-right: 3em;

    z-index: 100;
}

.close_modal:hover {
    cursor: pointer;
}

.close_modal>i {
    margin: 1em;
    font-size: 1.5em;
}

ul {
    height: 100%;
    overflow-y: scroll;

    list-style-type: none;

    padding: 2em;
}

.title {
    margin: 1em;
}
</style>