<template>
  <div class="w3-container w3-card-4" style="min-width:300px; max-width:300px; display:inline-block; vertical-align:top">
    <div>
      <h3 style="overflow: hidden; text-overflow: ellipsis; max-width:300px"><strong>Campo: </strong>{{Campo.Nombre}}</h3>
      <label class="w3-text" for="nombre"> Nombre </label>
      <textarea class="w3-input w3-border" rows="2" style="background: white; white-space: nowrap; overflow-x: auto; resize: none; text-overflow: ellipsis" type="string" name="nombre" value="Nombre" :disabled="!editing && !addingNew" v-model="Campo.Nombre"></textarea>
      <br>
      <label class="w3-text" for="tipo"> Tipo </label>
      <select class="w3-select w3-border" name="tipo" style="overflow: hidden; text-overflow: ellipsis" value="Tipo" :disabled="!editing && !addingNew" v-model="Campo.Tipo">
        <option value="string">String</option>
        <option value="double">Double</option>
        <option value="int">Int</option>
        <option value="float">Float</option>
      </select>
      <br>

      <template v-if="Campo.Tipo == 'string'">
        <div style="float: left">
          <br>
          <label class="w3-text" for="maxLength"> Longitud Máxima </label>
          <input class="w3-input w3-border" style="background: white; white-space: nowrap; overflow-x: auto; resize: none; text-overflow: ellipsis" name="maxLength" value="MaxLength" :disabled="!editing && !addingNew" v-model="Campo.MaxLength"></textarea>
          <br>
          <label class="w3-text" for="minLength"> Longitud Mínima </label>
          <input class="w3-input w3-border" style="background: white; white-space: nowrap; overflow-x: auto; resize: none; text-overflow: ellipsis" name="minLength" value="MinLength" :disabled="!editing && !addingNew" v-model="Campo.MinLength"></textarea>
          <br>
        </div>
      </template>

      <template v-if="Campo.Tipo == 'double' || Campo.Tipo == 'int' || Campo.Tipo == 'float'">
        <div style="float: left">
          <br>
          <label class="w3-text" for="maxLength"> Valor Máximo </label>
          <input class="w3-input w3-border" style="background: white; white-space: nowrap; overflow-x: auto; resize: none; text-overflow: ellipsis" name="maxValue" value="MaxValue" :disabled="!editing && !addingNew" v-model="Campo.MaxValue"></textarea>
          <br>
          <label class="w3-text" for="minLength"> Valor Mínimo </label>
          <input class="w3-input w3-border" style="background: white; white-space: nowrap; overflow-x: auto; resize: none; text-overflow: ellipsis" name="minValue" value="MinValue" :disabled="!editing && !addingNew" v-model="Campo.MinValue"></textarea>
          <br>
        </div>
      </template>


      <br>
      <label class="w3-text" for="tareaAsociada"> Tarea Asociada </label>
      <textarea class="w3-input w3-border" rows="2" style="background: white; white-space: nowrap; overflow-x: auto; resize: none; text-overflow: ellipsis" type="string" name="tareaAsociada" value="TareaAsociada" :disabled="!editing && !addingNew" v-model="Campo.TareaAsociada"></textarea>

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
var minInt = -2147483648;
var minFloat = Number.MIN_VALUE;
var maxFloat = Number.MAX_VALUE;

export default {
  components: {
    'app-icon' : AppIcon,
    'infomessage' : InfoMessage
  },

  methods: {
    validateTipoString: function(){
      let mensaje ='';
      let validated = false;
      if (!this.isInt(this.Campo.MaxLength) || this.Campo.MaxLength <= 0) {
        mensaje = 'La longitud máxima debe ser un número entero mayor que 0.';
        EventBus.$emit('showMessage', mensaje);
      } else if (!this.isInt(this.Campo.MinLength) || this.Campo.MinLength < 0) {
        mensaje = 'La longitud mínima debe ser un número entero mayor o igual que 0.';
        EventBus.$emit('showMessage', mensaje);
      } else if (this.Campo.MinLength >= this.Campo.MaxLength) {
        mensaje = 'La longitud mínima debe ser menor que la longitud máxima.';
        EventBus.$emit('showMessage', mensaje);
      } else {
        validated = true;
      }
      return validated;
    },
    validateTipoInt: function(){
      let mensaje ='';
      let validated = false;
      if (!this.isInt(this.Campo.MaxValue) || this.Campo.MaxValue > maxInt || this.Campo.MaxValue < minInt) {
        mensaje = 'El valor máximo debe ser un número entero en el rango del tipo int.';
        EventBus.$emit('showMessage', mensaje);
      } else if (!this.isInt(this.Campo.MinValue) || this.Campo.MinValue > maxInt || this.Campo.MinValue < minInt) {
        mensaje = 'El valor mínimo debe ser un número entero en el rango del tipo int.';
        EventBus.$emit('showMessage', mensaje);
      } else if (parseInt(this.Campo.MinValue) >= parseInt(this.Campo.MaxValue)) {
        mensaje = 'El valor mínimo debe ser menor que el valor máximo.';
        EventBus.$emit('showMessage', mensaje);
      } else {
        validated = true;
      }
      return validated;
    },
    validateTipoFloat: function(){
      let mensaje ='';
      let validated = false;
      if (!this.isNumeric(parseFloat(this.Campo.MaxValue))) {
        mensaje = 'El valor máximo debe ser un número en coma flotante.';
        EventBus.$emit('showMessage', mensaje);
      } else if (!this.isNumeric(parseFloat(this.Campo.MinValue))) {
        mensaje = 'El valor mínimo debe ser un número en coma flotante.';
        EventBus.$emit('showMessage', mensaje);
      } else if (parseFloat(this.Campo.MinValue) >= parseFloat(this.Campo.MaxValue)) {
        mensaje = 'El valor mínimo debe ser menor que el valor máximo.';
        EventBus.$emit('showMessage', mensaje);
      } else {
        validated = true;
      }
      return validated;
    },
    validateTipoDouble: function(){
      let mensaje ='';
      let validated = false;
      if (!this.isNumeric(parseFloat(this.Campo.MaxValue))) {
        mensaje = 'El valor máximo debe ser un número en coma flotante.';
        EventBus.$emit('showMessage', mensaje);
      } else if (!this.isNumeric(parseFloat(this.Campo.MinValue))) {
        mensaje = 'El valor mínimo debe ser un número en coma flotante.';
        EventBus.$emit('showMessage', mensaje);
      } else if (parseFloat(this.Campo.MinValue) >= parseFloat(this.Campo.MaxValue)) {
        mensaje = 'El valor mínimo debe ser menor que el valor máximo.';
        EventBus.$emit('showMessage', mensaje);
      } else {
        validated = true;
      }
      return validated;
    },
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
      } else if(this.Campo.Tipo == 'string') {
        if (this.validateTipoString()){
          this.create();
        }
      } else if(this.Campo.Tipo == 'int') {
        if (this.validateTipoInt()){
          this.create();
        }
      } else if(this.Campo.Tipo == 'double') {
        if (this.validateTipoDouble()){
          this.create();
        }
      } else if(this.Campo.Tipo == 'float') {
        if (this.validateTipoFloat()){
          this.create();
        }
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
      } else if(this.Campo.Tipo == 'string') {
        if (this.validateTipoString()){
          this.update();
        }
      } else if(this.Campo.Tipo == 'int') {
        if (this.validateTipoInt()){
          this.update();
        }
      } else if(this.Campo.Tipo == 'double') {
        if (this.validateTipoDouble()){
          this.update();
        }
      } else if(this.Campo.Tipo == 'float') {
        if (this.validateTipoDouble()){
          this.update();
        }
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
      this.CampoCopia.MinValue = this.Campo.MinValue;
      this.CampoCopia.MaxValue = this.Campo.MaxValue;
      this.CampoCopia.MinLength = this.Campo.MinLength;
      this.CampoCopia.MaxLength = this.Campo.MaxLength;

      this.Campo.Nombre = '';
      this.Campo.Tipo = '';
      this.Campo.TareaAsociada = '';
      this.Campo.MinValue = null;
      this.Campo.MaxValue = null;
      this.Campo.MinLength = null;
      this.Campo.MaxLength = null;
      this.addingNew = true;
    },
    discardNew: function () {
      this.Campo.Nombre = this.CampoCopia.Nombre;
      this.Campo.Tipo = this.CampoCopia.Tipo;
      this.Campo.TareaAsociada = this.CampoCopia.TareaAsociada;
      this.Campo.MinValue = this.CampoCopia.MinValue;
      this.Campo.MaxValue = this.CampoCopia.MaxValue;
      this.Campo.MinLength = this.CampoCopia.MinLength;
      this.Campo.MaxLength = this.CampoCopia.MaxLength;
      this.addingNew = false;
    },
    edit: function () {
      this.CampoCopia.Nombre = this.Campo.Nombre;
      this.CampoCopia.Tipo = this.Campo.Tipo;
      this.CampoCopia.TareaAsociada = this.Campo.TareaAsociada;
      this.CampoCopia.MinValue = this.Campo.MinValue;
      this.CampoCopia.MaxValue = this.Campo.MaxValue;
      this.CampoCopia.MinLength = this.Campo.MinLength;
      this.CampoCopia.MaxLength = this.Campo.MaxLength;

      this.editing = true;
    },
    discard: function () {
      this.Campo.Nombre = this.CampoCopia.Nombre;
      this.Campo.Tipo = this.CampoCopia.Tipo;
      this.Campo.TareaAsociada = this.CampoCopia.TareaAsociada;
      this.Campo.MinValue = this.CampoCopia.MinValue;
      this.Campo.MaxValue = this.CampoCopia.MaxValue;
      this.Campo.MinLength = this.CampoCopia.MinLength;
      this.Campo.MaxLength = this.CampoCopia.MaxLength;
      this.editing = false;
    },
    cleanForm: function() {
      this.Campo.Nombre = '';
      this.Campo.Tipo = '';
      this.Campo.TareaAsociada = '';
      this.Campo.Id = '';
      this.Campo.MinValue = null;
      this.Campo.MaxValue = null;
      this.Campo.MinLength = null;
      this.Campo.MaxLength = null;
    },
    controlNulls: function() {
      if (this.Campo.Tipo == 'string'){
        this.Campo.MaxValue = null;
        this.Campo.MinValue = null;
      } else if (this.Campo.Tipo == 'double' || this.Campo.Tipo == 'int' || this.Campo.Tipo == 'float'){
        this.Campo.MaxLength = null;
        this.Campo.MinLength = null;
      }
    },
    create: function () {
      var _this = this;
      _this.controlNulls();
      $.ajax(
        {
          url : httpURL,
          type: "POST",
          data: {
            Nombre: this.Campo.Nombre,
            Tipo: this.Campo.Tipo,
            TareaAsociada: this.Campo.TareaAsociada,
            MaxValue: this.Campo.MaxValue,
            MinValue: this.Campo.MinValue,
            MaxLength: this.Campo.MaxLength,
            MinLength: this.Campo.MinLength
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
        _this.controlNulls();
        $.ajax(
          {
            url : httpURL + this.Campo.Id,
            type: "PUT",
            data: {
              Id: this.Campo.Id,
              Nombre: this.Campo.Nombre,
              Tipo: this.Campo.Tipo,
              TareaAsociada: this.Campo.TareaAsociada,
              MaxValue: this.Campo.MaxValue,
              MinValue: this.Campo.MinValue,
              MaxLength: this.Campo.MaxLength,
              MinLength: this.Campo.MinLength
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
                MaxValue: null,
                MinValue: null,
                MaxLength: null,
                MinLength: null,
                TareaAsociada: ''
              },
              CampoCopia: {
                Id: '',
                Nombre: '',
                Tipo: '',
                MaxValue: null,
                MinValue: null,
                MaxLength: null,
                MinLength: null,
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
