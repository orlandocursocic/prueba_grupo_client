<template>
  <div class="w3-container w3-card-4" style="min-width:300px; max-width:300px; display:inline-block; vertical-align:top">
    <div>
      <h3 style="overflow: hidden; text-overflow: ellipsis; max-width:300px"><strong>Campo: </strong>{{Campo.Nombre}}</h3>
      <label class="w3-text" for="nombre"> Nombre </label>
      <textarea class="w3-input w3-border" rows="2" style="white-space: nowrap; overflow-x: auto; resize: none; text-overflow: ellipsis" type="string" name="nombre" value="Nombre" :disabled="!editing && !addingNew" v-model="Campo.Nombre"></textarea>
      <br>
      <label class="w3-text" for="tipo"> Tipo </label>
      <textarea class="w3-input w3-border" rows="2" style="white-space: nowrap; overflow-x: auto; resize: none; text-overflow: ellipsis" type="string" name="tipo" value="Tipo" :disabled="!editing && !addingNew" v-model="Campo.Tipo"></textarea>
      <br>
      <label class="w3-text" for="tareaAsociada"> Tarea Asociada </label>
      <textarea class="w3-input w3-border" rows="2" style="white-space: nowrap; overflow-x: auto; resize: none; text-overflow: ellipsis" type="string" name="tareaAsociada" value="TareaAsociada" :disabled="!editing && !addingNew" v-model="Campo.TareaAsociada"></textarea>
    </div>

    <br>

    <div>
      <template v-if="!addingNew">
        <div style="float: left">
          <button type="button" class="btn btn-default btn-sm" title="Nuevo" @click="editNew" :disabled="editing">
            <app-icon img="plus"></app-icon>
          </button>
        </div>
      </template>

      <template v-else>
        <div style="float: left">
          <button type="button" class="btn btn-default btn-sm" title="Confirmar" @click="validateNew">
            <app-icon img="ok"></app-icon>
          </button>
          <button type="button" class="btn btn-default btn-sm" title="Descartar" @click="discardNew">
            <app-icon img="remove"></app-icon>
          </button>
        </div>
      </template>

      <template v-if="!editing">
        <div style="float: right">
          <button type="button" class="btn btn-default btn-sm" title="Editar" @click="validateIdUpdate" :disabled="addingNew">
            <app-icon img="edit"></app-icon>
          </button>
          <button type="button" class="btn btn-default btn-sm" title="Eliminar" @click="validateIdDelete" :disabled="addingNew">
            <app-icon img="trash"></app-icon>
          </button>
        </div>
      </template>

      <template v-else>
        <div style="float: right">
          <button type="button" class="btn btn-default btn-sm" title="Confirmar" @click="validateUpdate">
            <app-icon img="ok"></app-icon>
          </button>
          <button type="button" class="btn btn-default btn-sm" title="Descartar" @click="discard">
            <app-icon img="remove"></app-icon>
          </button>
        </div>
      </template>
    </div>

    <div>
      <p style="visibility: hidden">separador</p>
    </div>
    <br>
  </div>
</template>

<script>
import EventBus from './event-bus.js'
import AppIcon from './App-icon.vue'
import appConfig from './config.js'
import InfoMessage from './InfoMessage.vue'

var httpURL = appConfig.URLCampo;
var maxInt =  2147483647;

export default {
  components: {
    'app-icon' : AppIcon,
    'infomessage' : InfoMessage
  },

  methods: {
    validateNew: function() {
      let mensaje ='';
      if(this.Campo.Nombre == '') {
        mensaje = 'El nombre del campo no puede estar vacío.';
        EventBus.$emit('showMessage', mensaje);
      } else if(this.Campo.Tipo == '') {
        mensaje = 'El tipo del campo no puede estar vacío.';
        EventBus.$emit('showMessage', mensaje);
      } else if(this.Campo.TareaAsociada == '') {
        mensaje = 'El nombre de la tarea asociada no puede estar vacío.';
        EventBus.$emit('showMessage', mensaje);
      } else {
        this.create();
      }
    },
    validateIdUpdate: function() {
      let mensaje ='';
      if(this.Campo.Id =='' || this.Campo.Id < 0) {
        mensaje = 'Seleccione un campo de la lista.';
        EventBus.$emit('showMessage', mensaje);
      } else {
        this.edit();
      }
    },
    validateIdDelete: function() {
      let mensaje ='';
      if(this.Campo.Id =='' || this.Campo.Id < 0) {
        mensaje = 'Seleccione un campo de la lista.';
        EventBus.$emit('showMessage', mensaje);
      } else {
        this.remove();
      }
    },
    validateUpdate: function() {
      let mensaje ='';
      if(this.Campo.Nombre == '') {
        mensaje = 'El nombre del campo no puede estar vacío.';
        EventBus.$emit('showMessage', mensaje);
      } else if(this.Campo.Tipo == '') {
        mensaje = 'El tipo del campo no puede estar vacío.';
        EventBus.$emit('showMessage', mensaje);
      } else if(this.Campo.TareaAsociada == '') {
        mensaje = 'El nombre de la tarea asociada no puede estar vacío.';
        EventBus.$emit('showMessage', mensaje);
      } else {
        this.update();
      }
    },
    isNumeric: function(n) {
      return !isNaN(parseFloat(n)) && isFinite(n);
    },
    isInt: function(n) {
      return n % 1 === 0;
    },
    editNew: function () {
      this.CampoCopia.Nombre = this.Campo.Nombre;
      this.CampoCopia.Tipo = this.Campo.Tipo;
      this.CampoCopia.TareaAsociada = this.Campo.TareaAsociada;

      this.Campo.Nombre = '';
      this.Campo.Tipo = '';
      this.Campo.TareaAsociada = '';
      this.addingNew = true;
    },
    discardNew: function () {
      this.Campo.Nombre = this.CampoCopia.Nombre;
      this.Campo.Tipo = this.CampoCopia.Tipo;
      this.Campo.TareaAsociada = this.CampoCopia.TareaAsociada;
      this.addingNew = false;
    },
    edit: function () {
      this.CampoCopia.Nombre = this.Campo.Nombre;
      this.CampoCopia.Tipo = this.Campo.Tipo;
      this.CampoCopia.TareaAsociada = this.Campo.TareaAsociada;
      this.editing = true;
    },
    discard: function () {
      this.Campo.Nombre = this.CampoCopia.Nombre;
      this.Campo.Tipo = this.CampoCopia.Tipo;
      this.Campo.TareaAsociada = this.CampoCopia.TareaAsociada;
      this.editing = false;
    },
    cleanForm: function() {
      this.Campo.Nombre = '';
      this.Campo.Tipo = '';
      this.Campo.TareaAsociada = '';
      this.Campo.Id = '';
    },
    create: function () {
      var _this = this;
      $.ajax(
        {
          url : httpURL,
          type: "POST",
          data: {
            Nombre: this.Campo.Nombre,
            Tipo: this.Campo.Tipo,
            TareaAsociada: this.Campo.TareaAsociada
          }

        })
        .done(function(data) {
          EventBus.$emit('updateListCampo');
          let mensaje ='Campo añadido con éxito.';
          EventBus.$emit('showMessage', mensaje);
          _this.addingNew = false;
        })
        .fail(function(data) {
          let mensaje = 'No se pudo crear el Campo. Revise su conexión a Internet.';
          EventBus.$emit('showMessage', mensaje);
        });
      },
      update: function () {
        var _this = this;
        $.ajax(
          {
            url : httpURL + this.Campo.Id,
            type: "PUT",
            data: {
              Id: this.Campo.Id,
              Nombre: this.Campo.Nombre,
              Tipo: this.Campo.Tipo,
              TareaAsociada: this.Campo.TareaAsociada
            }
          })
          .done(function(data) {
            EventBus.$emit('updateListCampo');
            let mensaje ='Campo actualizado con éxito.';
            EventBus.$emit('showMessage', mensaje);
            _this.cleanForm();
            _this.editing = false;
          })
          .fail(function(data) {
            let mensaje = 'No se pudo actualizar el Campo. Revise su conexión a Internet.';
            EventBus.$emit('showMessage', mensaje);
          });
        },
        remove: function () {
          var _this = this;
          $.ajax(
            {
              url : httpURL + this.Campo.Id,
              type: "DELETE",
              data: {Id: this.Campo.Id}
            })
            .done(function(data) {
              EventBus.$emit('updateListCampo');
              _this.cleanForm();
              let mensaje ='Campo eliminado con éxito.';
              EventBus.$emit('showMessage', mensaje);
            })
            .fail(function(data) {
              let mensaje = 'No se pudo eliminar el campo. Revise su conexión a Internet.';
              EventBus.$emit('showMessage', mensaje);
            });
            this.editing = false;
          },
          load: function(id){
            var _this = this;
            $.ajax(
              {
                url : httpURL + id,
                type: "GET",
              })
              .done(function(data) {
                _this.Campo = data;
              })
              .fail(function(data) {
                let mensaje = 'No se pudo cargar el Campo. Revise su conexión a Internet.';
                EventBus.$emit('showMessage', mensaje);
              });
            },
          },

          data: function () {
            return {
              editing: false,
              addingNew: false,
              Campo: {
                Id: '',
                Nombre: '',
                Tipo: '',
                TareaAsociada: ''
              },
              CampoCopia: {
                Id: '',
                Nombre: '',
                Tipo: '',
                TareaAsociada: ''
              }
            }
          },

          mounted: function() {
            EventBus.$on('campoSelected', function(id) {
              this.load(id);
              this.editing = false;
              this.addingNew = false;
            }.bind(this));
          }
        }

        </script>
