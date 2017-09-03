<template>
  <div class="w3-container w3-card-4" style="min-width:300px; max-width:300px; display:inline-block; vertical-align:top">
    <div>
      <h3 style="overflow: hidden; text-overflow: ellipsis; max-width:300px"><strong>Tarea: </strong>{{Tarea.Nombre}}</h3>
      <label class="w3-text" for="nombre"> Nombre </label>
      <input class="w3-input w3-border" style="overflow: hidden; text-overflow: ellipsis" type="string" name="nombre" value="Nombre" :disabled="!editing && !addingNew" v-model="Tarea.Nombre">
      <label class="w3-text" for="desc"> Descripcion </label>
      <input class="w3-input w3-border" style="overflow: hidden; text-overflow: ellipsis" type="string" name="desc" value="Descripcion" :disabled="!editing && !addingNew" v-model="Tarea.Descripcion">
      <label class="w3-text" for="fecha"> Fecha </label>
      <input class="w3-input w3-border" style="overflow: hidden; text-overflow: ellipsis" type="string" name="fecha" value="Fecha" :disabled="!editing && !addingNew" v-model="Tarea.Fecha">
      <label class="w3-text" for="complejidad"> Complejidad </label>
      <input class="w3-input w3-border" style="overflow: hidden; text-overflow: ellipsis" type="string" name="complejidad" 
      value="Complejidad" :disabled="!editing && !addingNew" v-model="Tarea.Complejidad">
      <label class="w3-text" for="estimacion"> Estimacion </label>
      <input class="w3-input w3-border" style="overflow: hidden; text-overflow: ellipsis" type="numeric" name="estimacion" 
      value="Estimacion" :disabled="!editing && !addingNew" v-model="Tarea.Estimacion">
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

var httpURL = appConfig.URLTarea;
var maxInt =  2147483647;

export default {
  components: {
    'app-icon' : AppIcon,
    'infomessage' : InfoMessage
  },

  methods: {
    validateNew: function() {
      let mensaje ='';
      if(this.Tarea.Nombre == '') {
        mensaje = 'El Nombre de la tarea no puede estar vacío.';
        EventBus.$emit('showMessage', mensaje);
      } else if(!this.isInt(this.Tarea.Estimacion) || this.Tarea.Estimacion <= 0) {
        mensaje = 'La Estimacion de la Tarea debe ser un número entero mayor que 0.';
        EventBus.$emit('showMessage', mensaje);
      } else if(this.isNumeric(this.Tarea.Descripcion) || this.Tarea.Descripcion == '') {
        mensaje = 'La Descripcion de la tarea no puede ser un número ni estar vacío.';
        EventBus.$emit('showMessage', mensaje);
      } else if(this.isNumeric(this.Tarea.Fecha) || this.Tarea.Fecha == '') {
        mensaje = 'La Fecha de la tarea no puede ser un número ni estar vacío';
        EventBus.$emit('showMessage', mensaje);
      } else if(this.isNumeric(this.Tarea.Complejidad) || this.Tarea.Complejidad == '') {
        mensaje = 'La Complejidad de la tarea no puede ser un número ni estar vacío';
        EventBus.$emit('showMessage', mensaje);
      } else {
        this.create();
      }
    },
    validateIdUpdate: function() {
      let mensaje ='';
      if(this.Tarea.Id =='' || this.Tarea.Id < 0) {
        mensaje = 'Seleccione una Tarea de la lista.';
        EventBus.$emit('showMessage', mensaje);
      } else {
        this.edit();
      }
    },
    validateIdDelete: function() {
      let mensaje ='';
      if(this.Tarea.Id =='' || this.Tarea.Id < 0) {
        mensaje = 'Seleccione una Tarea de la lista.';
        EventBus.$emit('showMessage', mensaje);
      } else {
        this.remove();
      }
    },
    validateUpdate: function() {
      let mensaje ='';
      if(this.Tarea.Nombre == '') {
        mensaje = 'El Nombre de la tarea no puede estar vacío.';
        EventBus.$emit('showMessage', mensaje);
      } else if(!this.isInt(this.Tarea.Estimacion) || this.Tarea.Estimacion <= 0) {
        mensaje = 'La Estimacion de la Tarea debe ser un número entero mayor que 0.';
        EventBus.$emit('showMessage', mensaje);
      } else if(this.isNumeric(this.Tarea.Descripcion) || this.Tarea.Descripcion == '') {
        mensaje = 'La Descripcion de la tarea no puede ser un número ni estar vacío.';
        EventBus.$emit('showMessage', mensaje);
      } else if(this.isNumeric(this.Tarea.Fecha) || this.Tarea.Fecha == '') {
        mensaje = 'La Fecha de la tarea no puede ser un número ni estar vacío';
        EventBus.$emit('showMessage', mensaje);
      } else if(this.isNumeric(this.Tarea.Complejidad) || this.Tarea.Complejidad == '') {
        mensaje = 'La Complejidad de la tarea no puede ser un número ni estar vacío';
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
      this.TareaCopia.Nombre = this.Tarea.Nombre;
      this.TareaCopia.Descripcion = this.Tarea.Descripcion;
      this.TareaCopia.Fecha = this.Tarea.Fecha;
      this.TareaCopia.Complejidad = this.Tarea.Complejidad;
      this.TareaCopia.Estimacion = this.Tarea.Estimacion;

      this.Tarea.Nombre = '';
      this.Tarea.Descripcion = '';
      this.Tarea.Fecha = '';
      this.Tarea.Complejidad = '';
      this.Tarea.Estimacion = '';
      this.addingNew = true;
    },
    discardNew: function () {
      this.Tarea.Nombre = this.TareaCopia.Nombre;
      this.Tarea.Descripcion = this.TareaCopia.Descripcion;
      this.Tarea.Fecha = this.TareaCopia.Fecha;
      this.Tarea.Complejidad = this.TareaCopia.Complejidad;
      this.Tarea.Estimacion = this.TareaCopia.Estimacion;
      this.addingNew = false;
    },
    edit: function () {
       this.TareaCopia.Nombre = this.Tarea.Nombre;
      this.TareaCopia.Descripcion = this.Tarea.Descripcion;
      this.TareaCopia.Fecha = this.Tarea.Fecha;
      this.TareaCopia.Complejidad = this.Tarea.Complejidad;
      this.TareaCopia.Estimacion = this.Tarea.Estimacion;
      this.editing = true;
    },
    discard: function () {
      this.Tarea.Nombre = this.TareaCopia.Nombre;
      this.Tarea.Descripcion = this.TareaCopia.Descripcion;
      this.Tarea.Fecha = this.TareaCopia.Fecha;
      this.Tarea.Complejidad = this.TareaCopia.Complejidad;
      this.Tarea.Estimacion = this.TareaCopia.Estimacion;
      this.editing = false;
    },
    cleanForm: function() {
      this.Tarea.Nombre = '';
      this.Tarea.Descripcion = '';
      this.Tarea.Fecha = '';
      this.Tarea.Complejidad = '';
      this.Tarea.Estimacion = '';
      this.Tarea.Id = '';
    },
    create: function () {
      var _this = this;
      $.ajax(
        {
          url : httpURL,
          type: "POST",
          data: {
            Nombre: this.Tarea.Nombre,
            Descripcion: this.Tarea.Descripcion,
            Fecha: this.Tarea.Fecha,
            Complejidad: this.Tarea.Complejidad,
            Estimacion: this.Tarea.Estimacion
          }

        })
        .done(function(data) {
          EventBus.$emit('updateListTarea');
          let mensaje ='Tarea añadida con éxito.';
          EventBus.$emit('showMessage', mensaje);
          _this.addingNew = false;
        })
        .fail(function(data) {
          let mensaje = 'No se pudo crear la Tarea. Revise su conexión a Internet.';
          EventBus.$emit('showMessage', mensaje);
        });
      },
      update: function () {
        var _this = this;
        $.ajax(
          {
            url : httpURL + this.Tarea.Id,
            type: "PUT",
            data: {
              Id: this.Tarea.Id,
              Nombre: this.Tarea.Nombre,
              Descripcion: this.Tarea.Descripcion,
              Fecha: this.Tarea.Fecha,
              Complejidad: this.Tarea.Complejidad,
              Estimacion: this.Tarea.Estimacion
            }
          })
          .done(function(data) {
            EventBus.$emit('updateListTarea');
            let mensaje ='Tarea actualizada con éxito.';
            EventBus.$emit('showMessage', mensaje);
            _this.cleanForm();
            _this.editing = false;
          })
          .fail(function(data) {
            let mensaje = 'No se pudo actualizar la Tarea. Revise su conexión a Internet.';
            EventBus.$emit('showMessage', mensaje);
          });
        },
        remove: function () {
          var _this = this;
          $.ajax(
            {
              url : httpURL + this.Tarea.Id,
              type: "DELETE",
              data: {Id: this.Tarea.Id}
            })
            .done(function(data) {
              EventBus.$emit('updateListTarea');
              _this.cleanForm();
              let mensaje ='Tarea eliminada con éxito.';
              EventBus.$emit('showMessage', mensaje);
              _this.editing = false;
            })
            .fail(function(data) {
              let mensaje = 'No se pudo eliminar la Tarea. Revise su conexión a Internet.';
              EventBus.$emit('showMessage', mensaje);
            });
          },
          load: function(id){
            var _this = this;
            $.ajax(
              {
                url : httpURL + id,
                type: "GET",
              })
              .done(function(data) {
                _this.Tarea = data;
              })
              .fail(function(data) {
                let mensaje = 'No se pudo cargar la Tarea. Revise su conexión a Internet.';
                EventBus.$emit('showMessage', mensaje);
              });
            },
          },

          data: function () {
            return {
              editing: false,
              addingNew: false,
              Tarea: {
                Id: '',
                Nombre: '',
                Descripcion: '',
                Fecha: '',
                Complejidad: '',
                Estimacion: ''
              },
              TareaCopia: {
                Id: '',
                Nombre: '',
                Descripcion: '',
                Fecha: '',
                Complejidad: '',
                Estimacion: ''
              }
            }
          },

          mounted: function() {
            EventBus.$on('tareaSelected', function(id) {
              this.load(id);
              this.editing = false;
              this.addingNew = false;
            }.bind(this));
          }
        }

        </script>
