<template>
  <div class="w3-container w3-card-4" style="min-width:300px; max-width:300px; display:inline-block; vertical-align:top">
    <div>
      <h3 style="overflow: hidden; text-overflow: ellipsis; max-width:300px"><strong>Entrada, precio: </strong>{{Entrada.Precio}}</h3>
      <label class="w3-text" for="precio"> Precio </label>
      <input class="w3-input w3-border" type="numeric" name="precio" value="Precio" :disabled="!editing && !addingNew" v-model="Entrada.Precio">
      <label class="w3-text" for="sala"> Sala </label>
      <input class="w3-input w3-border" type="numeric" name="sala" value="Sala" :disabled="!editing && !addingNew" v-model="Entrada.Sala">
      <label class="w3-text" for="butaca"> Butaca </label>
      <input class="w3-input w3-border" type="numeric" name="butaca" value="Butaca" :disabled="!editing && !addingNew" v-model="Entrada.Butaca">
      <label class="w3-text" for="fila"> Fila </label>
      <input class="w3-input w3-border" type="numeric" name="fila" value="Fila" :disabled="!editing && !addingNew" v-model="Entrada.Fila">
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

var httpURL = appConfig.URLEntrada;
var maxInt =  2147483647;

export default {
  components: {
    'app-icon' : AppIcon,
    'infomessage' : InfoMessage
  },

  methods: {
    validateNew: function() {
      let mensaje ='';
      if(!this.isNumeric(this.Entrada.Precio) || this.Entrada.Precio <= 0) {
        mensaje = 'El Precio debe ser un número mayor que 0.';
        EventBus.$emit('showMessage', mensaje);
      } else if(!this.isInt(this.Entrada.Sala) || this.Entrada.Sala <= 0) {
        mensaje = 'La Sala debe ser un número entero mayor que 0.';
        EventBus.$emit('showMessage', mensaje);
      } else if(!this.isInt(this.Entrada.Butaca) || this.Entrada.Butaca <= 0) {
        mensaje = 'La Butaca debe ser un número entero mayor que 0.';
        EventBus.$emit('showMessage', mensaje);
      } else if(!this.isInt(this.Entrada.Fila) || this.Entrada.Fila <= 0) {
        mensaje = 'La Fila debe ser un número entero mayor que 0.';
        EventBus.$emit('showMessage', mensaje);
      } else if(this.Entrada.Sala > maxInt) {
        mensaje = 'La Sala debe ser un número menor que ' + maxInt;
        EventBus.$emit('showMessage', mensaje);
      } else if(this.Entrada.Butaca > maxInt) {
        mensaje = 'La Butaca debe ser un número menor que ' + maxInt;
        EventBus.$emit('showMessage', mensaje);
      } else if(this.Entrada.Fila > maxInt) {
        mensaje = 'La Fila debe ser un número menor que ' + maxInt;
        EventBus.$emit('showMessage', mensaje);
      } else {
        this.create();
      }
    },
    validateIdUpdate: function() {
      let mensaje ='';
      if(this.Entrada.Id =='' || this.Entrada.Id < 0) {
        mensaje = 'Seleccione una entrada de la lista.';
        EventBus.$emit('showMessage', mensaje);
      } else {
        this.edit();
      }
    },
    validateIdDelete: function() {
      let mensaje ='';
      if(this.Entrada.Id =='' || this.Entrada.Id < 0) {
        mensaje = 'Seleccione una entrada de la lista.';
        EventBus.$emit('showMessage', mensaje);
      } else {
        this.remove();
      }
    },
    validateUpdate: function() {
      let mensaje ='';
      if(!this.isNumeric(this.Entrada.Precio) || this.Entrada.Precio <= 0) {
        mensaje = 'El Precio debe ser un número mayor que 0.';
        EventBus.$emit('showMessage', mensaje);
      } else if(!this.isInt(this.Entrada.Sala) || this.Entrada.Sala <= 0) {
        mensaje = 'La Sala debe ser un número entero mayor que 0.';
        EventBus.$emit('showMessage', mensaje);
      } else if(!this.isInt(this.Entrada.Butaca) || this.Entrada.Butaca <= 0) {
        mensaje = 'La Butaca debe ser un número entero mayor que 0.';
        EventBus.$emit('showMessage', mensaje);
      } else if(!this.isInt(this.Entrada.Fila) || this.Entrada.Fila <= 0) {
        mensaje = 'La Fila debe ser un número entero mayor que 0.';
        EventBus.$emit('showMessage', mensaje);
      } else if(this.Entrada.Sala > maxInt) {
        mensaje = 'La Sala debe ser un número menor que ' + maxInt;
        EventBus.$emit('showMessage', mensaje);
      } else if(this.Entrada.Butaca > maxInt) {
        mensaje = 'La Butaca debe ser un número menor que ' + maxInt;
        EventBus.$emit('showMessage', mensaje);
      } else if(this.Entrada.Fila > maxInt) {
        mensaje = 'La Fila debe ser un número menor que ' + maxInt;
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
      this.EntradaCopia.Precio = this.Entrada.Precio;
      this.EntradaCopia.Sala = this.Entrada.Sala;
      this.EntradaCopia.Butaca = this.Entrada.Butaca;
      this.EntradaCopia.Fila = this.Entrada.Fila;

      this.Entrada.Precio = '';
      this.Entrada.Sala = '';
      this.Entrada.Butaca = '';
      this.Entrada.Fila = '';
      this.addingNew = true;
    },
    discardNew: function () {
      this.Entrada.Precio = this.EntradaCopia.Precio;
      this.Entrada.Sala = this.EntradaCopia.Sala;
      this.Entrada.Butaca = this.EntradaCopia.Butaca;
      this.Entrada.Fila = this.EntradaCopia.Fila;
      this.addingNew = false;
    },
    edit: function () {
      this.EntradaCopia.Precio = this.Entrada.Precio;
      this.EntradaCopia.Sala = this.Entrada.Sala;
      this.EntradaCopia.Butaca = this.Entrada.Butaca;
      this.EntradaCopia.Fila = this.Entrada.Fila;
      this.editing = true;
    },
    discard: function () {
      this.Entrada.Precio = this.EntradaCopia.Precio;
      this.Entrada.Sala = this.EntradaCopia.Sala;
      this.Entrada.Butaca = this.EntradaCopia.Butaca;
      this.Entrada.Fila = this.EntradaCopia.Fila;
      this.editing = false;
    },
    cleanForm: function() {
      this.Entrada.Precio = '';
      this.Entrada.Sala = '';
      this.Entrada.Butaca = '';
      this.Entrada.Fila = '';
      this.Entrada.Id = '';
    },
    create: function () {
      var _this = this;
      $.ajax(
        {
          url : httpURL,
          type: "POST",
          data: {
            Precio: this.Entrada.Precio,
            Sala: this.Entrada.Sala,
            Butaca: this.Entrada.Butaca,
            Fila: this.Entrada.Fila,
          }

        })
        .done(function(data) {
          EventBus.$emit('updateListEntrada');
          let mensaje ='Entrada añadida con éxito.';
          EventBus.$emit('showMessage', mensaje);
          _this.addingNew = false;
        })
        .fail(function(data) {
          let mensaje = 'No se pudo crear la Entrada. Revise su conexión a Internet.';
          EventBus.$emit('showMessage', mensaje);
        });
      },
      update: function () {
        var _this = this;
        $.ajax(
          {
            url : httpURL + this.Entrada.Id,
            type: "PUT",
            data: {
              Id: this.Entrada.Id,
              Precio: this.Entrada.Precio,
              Sala: this.Entrada.Sala,
              Butaca: this.Entrada.Butaca,
              Fila: this.Entrada.Fila
            }
          })
          .done(function(data) {
            EventBus.$emit('updateListEntrada');
            let mensaje ='Entrada actualizada con éxito.';
            EventBus.$emit('showMessage', mensaje);
            _this.cleanForm();
            _this.editing = false;
          })
          .fail(function(data) {
            let mensaje = 'No se pudo actualizar la Entrada. Revise su conexión a Internet.';
            EventBus.$emit('showMessage', mensaje);
          });
        },
        remove: function () {
          var _this = this;
          $.ajax(
            {
              url : httpURL + this.Entrada.Id,
              type: "DELETE",
              data: {Id: this.Entrada.Id}
            })
            .done(function(data) {
              EventBus.$emit('updateListEntrada');
              _this.cleanForm();
              let mensaje ='Entrada eliminada con éxito.';
              EventBus.$emit('showMessage', mensaje);
            })
            .fail(function(data) {
              let mensaje = 'No se pudo eliminar la Entrada. Revise su conexión a Internet.';
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
                _this.Entrada = data;
              })
              .fail(function(data) {
                let mensaje = 'No se pudo cargar la Entrada. Revise su conexión a Internet.';
                EventBus.$emit('showMessage', mensaje);
              });
            },
          },

          data: function () {
            return {
              editing: false,
              addingNew: false,
              Entrada: {
                Id: '',
                Precio: '',
                Sala: '',
                Butaca: '',
                Fila: ''
              },
              EntradaCopia: {
                Id: '',
                Precio: '',
                Sala: '',
                Butaca: '',
                Fila: ''
              }
            }
          },

          mounted: function() {
            EventBus.$on('entradaSelected', function(id) {
              this.load(id);
              this.editing = false;
              this.addingNew = false;
            }.bind(this));
          }
        }

        </script>
