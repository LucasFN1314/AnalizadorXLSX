<template>
  <div>
    <div class="container">

      <ProfesionalesModal ref="info_modal"></ProfesionalesModal>

      <div class="input-container">

        <div class="input-group mb-3">
          <input type="file" class="form-control" placeholder="Documento" id="input_file_1" v-on:change="read_data()">
        </div>

      </div>

      <div class="table-container">
        <table class="table" style="text-align:center;">
          <thead>
            <tr>
              <th scope="col">Departamento</th>
              <th scope="col">Realizadas</th>
              <th scope="col">Profesionales</th>
              <th scope="col">
                <button type="button" class="btn btn-outline-success" v-on:click="generate_excel()">Descargar</button>
              </th>
            </tr>
          </thead>
          <tbody>

            <tr v-for="(departamento, key) in departamentos" :key="key">
              <td>{{ departamento.nombre }}</td>
              <td>{{ departamento.realizadas }}</td>
              <td>
                <button type="button" class="btn btn-outline-success" v-on:click="this.$refs.info_modal.show(departamento)">Ver</button>
                </td>
                <td>
                <button type="button" class="btn btn-outline-success" v-on:click="generate_excel()">Descargar</button>
                </td>

            </tr>

          </tbody>
        </table>
      </div>

    </div>
  </div>
</template>

<script>
// eslint-disable-next-line no-unused-vars
import readXlsxFile from 'read-excel-file';
import $ from 'jquery';
import ProfesionalesModal from './ProfesionalesModal.vue';

class Departamento {
  constructor() {
    this.servicios = [];
    this.nombre = '';
    this.realizadas = 0;
  }
}

class Servicio {
  constructor() {
    this.profesionales = [];
    this.nombre = '';
    this.departamento = '';
    this.realizadas = 0;
  }
}

class Profesional {
  constructor() {
    this.servicio = [];
    this.realizadas = 0;
    this.nombre = '';
  }
}

export default {
  data() {
    return {
      info_length: 0,
      info: {},

      departamentos_agregados: [],
      servicios_agregados: [],
      profesionales_agregados: [],

      departamentos: [],
      servicios: [],
      profesionales: [],

      departamento_index: 2,
      servicio_index: 3,
      profesional_index: 4,
      estado_index: 5,
      cantidad_index: 6,
    }
  },
  methods: {

    read_data() {
      this.departamentos = [];
      readXlsxFile($('#input_file_1')[0].files[0]).then((rows) => {
        rows.splice(0, 1);
        rows.map((row) => {
          
          let departamento = row[this.departamento_index];
          if (this.check_dpto(departamento) == -1 && departamento !== null) {
            
            let Departament = new Departamento();
            Departament.nombre = departamento;

            this.departamentos.push(Departament);

            // Read all XLSX again?
            rows.map((row_2) => {
              if (row_2[this.departamento_index] == Departament.nombre) {
                if (this.check_service(row_2[this.servicio_index], Departament) == -1) {
                  let Service = new Servicio();
                  Service.nombre = row_2[this.servicio_index];
                  Departament.servicios.push(Service);

                  // Again, are u seriously?
                  rows.map((row_3) => {
                    if (row_3[this.servicio_index] == Service.nombre) {
                      if (this.check_profesional(Service, row_3[this.profesional_index]) == -1) {
                        let Profe = new Profesional();
                        Profe.nombre = row_3[this.profesional_index];

                        if(row_3[this.estado_index] == 'REALIZADO'){
                          Profe.realizadas += row_3[this.cantidad_index]; 
                          Departament.realizadas += row_3[this.cantidad_index];
                          Service.realizadas += row_3[this.cantidad_index];
                        }
                        Service.profesionales.push(Profe);

                      }
                      else {
                        if (row_3[this.estado_index] == 'REALIZADO') {
                          let index = this.check_profesional(Service, row_3[this.profesional_index]);
                          Service.profesionales[index].realizadas += row_3[this.cantidad_index];
                          Departament.realizadas += row_3[this.cantidad_index];
                          Service.realizadas += row_3[this.cantidad_index];

                        }
                      }
                    }
                  });
                }
              }
            });
          }

        });

        // Clean
        this.departamentos.map((departamento, dIndex) => {
          departamento.servicios.map((serv, sIndex) => {
            if(serv.realizadas == 0) {
              this.departamentos[dIndex].servicios.splice(sIndex, 1);
            }
          });

          if(departamento.realizadas === 0){
            this.departamentos[dIndex].splice(dIndex, 1);
          }
        });

      });
    },
    check_dpto(departamento) {
      let index = -1;
      this.departamentos.map((dep, Index) => {
        if (dep.nombre == departamento) {
          index = Index;
        }
      })
      return index;
    },
    check_service(service, dep) {
      let index = -1;

      dep.servicios.map((serv, sIndex) => {
        if (serv.nombre == service)
          index = sIndex;
      });

      return index;
    },
    check_profesional(service, profesional) {
      let index = -1;
      service.profesionales.map((prof, pIndex) => {
        if (prof.nombre == profesional) {
          index = pIndex;
        }
      });
      return index;
    },
    generate_excel() {
      console.log(this.departamentos);
    }

  },
  components: {
    ProfesionalesModal
  }
};
</script>

<style scoped>
td {}
</style>