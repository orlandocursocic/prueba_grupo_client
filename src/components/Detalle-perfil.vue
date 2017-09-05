<template>
	<div  class="w3-container w3-card-4" style="min-width:300px; max-width:300px; display:inline-block; vertical-align:top">
		<div>
		  <h3 style="overflow: hidden; text-overflow: ellipsis; max-width:400px"><strong>Perfil: </strong>{{Perfil.Nombre}}</h3>
		  
		  <label class="w3-text" for="nombre"> Nombre </label>
		  <textarea class="w3-input w3-border" rows="2" style="white-space: nowrap; overflow-x: auto; resize: none; text-overflow: ellipsis" type="string" name="nombre" value="Nombre" :disabled="!editing && !addingNew" v-model="Perfil.Nombre"></textarea>
	
		  <label class="w3-text" for="descripcion"> Descripción </label>
		  <textarea  class="w3-input w3-border" rows="3" style="resize: none; overflow: auto; text-overflow: ellipsis" type="string" name="descripcion" :disabled="!editing && !addingNew" v-model="Perfil.Descripcion"> </textarea>
		  
		  <label class="w3-text" for="departamento"> Departamento </label>
		  <input class="w3-input w3-border" style=" overflow: hidden; text-overflow: ellipsis" type="text" name="departamento" value="Departamento" :disabled="!editing && !addingNew" v-model="Perfil.Departamento">
		  
		  <label class="w3-text" for="edad-media"> Edad Media </label>
		  <input class="w3-input w3-border" style="overflow: hidden; text-overflow: ellipsis" name="edad-media" value="EdadMedia" :disabled="!editing && !addingNew" v-model="Perfil.EdadMedia">

		  <label class="w3-text"> Administrador </label>
		  <br>

		  <input type="radio" name="r_admin" :disabled="!editing && !addingNew" v-model="Perfil.Administrador" value="true"  :checked="Perfil.Administrador"> Si
		  <input type="radio" name="r_admin" :disabled="!editing && !addingNew" v-model="Perfil.Administrador" value="false" :checked="!Perfil.Administrador"> No 

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

	var httpURL = appConfig.URLPerfil;
	var maxInt =  2147483647;

	export default {
	  components: {
	    'app-icon' : AppIcon,
	    'infomessage' : InfoMessage
	  },	

	  methods:{
	  	validateNew: function() {
	  	  let mensaje ='';
	  	  if(this.Perfil.Nombre == '') {
	  	    mensaje = 'El nombre del perfil no puede estar vacío.';
	  	    EventBus.$emit('showMessage', mensaje);
	  	  } else if(this.Perfil.Descripcion == '') {
	  	    mensaje = 'La descripción del perfil no puede estar vacío.';
	  	    EventBus.$emit('showMessage', mensaje);
	  	  } else if(this.isNumeric(this.Perfil.Departamento) || this.Perfil.Departamento == '') {
	  	    mensaje = 'El nombre del departamento no puede contener un número ni estar vacío.';
	  	    EventBus.$emit('showMessage', mensaje);
	  	  } else if(!this.isInt(this.Perfil.EdadMedia) || this.Perfil.EdadMedia > 100 ) {
	  	    mensaje = 'La edad media debe ser númerico o no superar los 100 años';
	  	    EventBus.$emit('showMessage', mensaje);
	  	  } else {
	  	    this.create();
	  	  }
	  	},
	  	validateIdUpdate: function() {
	  	  let mensaje ='';
	  	  if(this.Perfil.Id =='' || this.Perfil.Id < 0) {
	  	    mensaje = 'Seleccione un perfil de la lista.';
	  	    EventBus.$emit('showMessage', mensaje);
	  	  } else {
	  	    this.edit();
	  	  }
	  	},
	  	validateIdDelete: function() {
	  	  let mensaje ='';
	  	  if(this.Perfil.Id =='' || this.Perfil.Id < 0) {
	  	    mensaje = 'Seleccione una película de la lista.';
	  	    EventBus.$emit('showMessage', mensaje);
	  	  } else {
	  	    this.remove();
	  	  }
	  	},
	  	validateUpdate: function() {
	  	  let mensaje ='';
  	  	  if(this.Perfil.Nombre == '') {
  	  	    mensaje = 'El nombre del perfil no puede estar vacío.';
  	  	    EventBus.$emit('showMessage', mensaje);
  	  	  } else if(this.Perfil.Descripcion == '') {
  	  	    mensaje = 'La descripción del perfil no puede estar vacío.';
  	  	    EventBus.$emit('showMessage', mensaje);
  	  	  } else if(this.isNumeric(this.Perfil.Departamento) || this.Perfil.Departamento == '') {
  	  	    mensaje = 'El nombre del departamento no puede contener un número ni estar vacío.';
  	  	    EventBus.$emit('showMessage', mensaje);
  	  	  } else if(!this.isInt(this.Perfil.EdadMedia) || this.Perfil.EdadMedia > 100 ) {
  	  	    mensaje = 'La edad media debe ser númerico o no superar los 100 años';
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
	  	  this.PerfilCopia.Nombre = this.Perfil.Nombre;
	  	  this.PerfilCopia.Descripcion = this.Perfil.Descripcion;
	  	  this.PerfilCopia.Departamento = this.Perfil.Departamento;
	  	  this.PerfilCopia.EdadMedia = this.Perfil.EdadMedia;
	  	  this.PerfilCopia.Administrador= this.Perfil.Administrador;

	  	  this.Perfil.Nombre = '';
	  	  this.Perfil.Descripcion = '';
	  	  this.Perfil.Departamento = '';
	  	  this.Perfil.EdadMedia = '';
	  	  this.Perfil.Administrador = '';
	  	  this.addingNew = true;
	  	},
	  	discardNew: function () {
	  	  this.Perfil.Nombre = this.PerfilCopia.Nombre;
	  	  this.Perfil.Descripcion = this.PerfilCopia.Descripcion;
	  	  this.Perfil.Departamento = this.PerfilCopia.Departamento;
	  	  this.Perfil.EdadMedia = this.PerfilCopia.EdadMedia;
	  	  this.Perfil.Administrador = this.PerfilCopia.Administrador;
	  	  this.addingNew = false;
	  	},
	  	edit: function () {
	  	  this.PerfilCopia.Nombre = this.Perfil.Nombre;
	  	  this.PerfilCopia.Descripcion = this.Perfil.Descripcion;
	  	  this.PerfilCopia.Departamento = this.Perfil.Departamento;
	  	  this.PerfilCopia.EdadMedia = this.Perfil.EdadMedia;
	  	  this.PerfilCopia.Administrador= this.Perfil.Administrador;
	  	  this.editing = true;
	  	},
	  	discard: function () {
	  	  this.Perfil.Nombre = this.PerfilCopia.Nombre;
	  	  this.Perfil.Descripcion = this.PerfilCopia.Descripcion;
	  	  this.Perfil.Departamento = this.PerfilCopia.Departamento;
	  	  this.Perfil.EdadMedia = this.PerfilCopia.EdadMedia;
	  	  this.Perfil.Administrador = this.PerfilCopia.Administrador;
	  	  this.editing = false;
	  	},
	  	cleanForm: function() {
	  	  this.Perfil.Nombre = '';
	  	  this.Perfil.Descripcion = '';
	  	  this.Perfil.Departamento = '';
	  	  this.Perfil.EdadMedia = '';
	  	  this.Perfil.Administrador = '';
	  	  this.Perfil.Id = '';
	  	},

	  	create: function () {
	  	  var _this = this;
	  	  $.ajax(
	  	    {
	  	      url : httpURL,
	  	      type: "POST",
	  	      data: {
	  	        Nombre: this.Perfil.Nombre,
	  	        Descripcion: this.Perfil.Descripcion,
	  	        Departamento: this.Perfil.Departamento,
	  	        EdadMedia: this.Perfil.EdadMedia,
	  	        Administrador: this.Perfil.Administrador,
	  	      }

	  	    })
	  	    .done(function(data) {
	  	      EventBus.$emit('updateListPerfil');
	  	      let mensaje ='Perfil añadido con éxito.';
	  	      EventBus.$emit('showMessage', mensaje);
	  	      _this.addingNew = false;
	  	    })
	  	    .fail(function(data) {
	  	      let mensaje = 'No se pudo crear el perfil. Revise su conexión a Internet.';
	  	      EventBus.$emit('showMessage', mensaje);
	  	    });
	  	},
	  	update: function () {
	  	  var _this = this;
	  	  $.ajax(
	  	    {
	  	      url : httpURL + this.Perfil.Id,
	  	      type: "PUT",
	  	      data: {
	  	        Id: this.Perfil.Id,
	  	        Nombre: this.Perfil.Nombre,
	  	        Descripcion: this.Perfil.Descripcion,
	  	        Departamento: this.Perfil.Departamento,
	  	        EdadMedia: this.Perfil.EdadMedia,
	  	        Administrador: this.Perfil.Administrador,
	  	      }
	  	    })
	  	    .done(function(data) {
	  	      EventBus.$emit('updateListPerfil');
	  	      let mensaje ='Perfil actualizado con éxito.';
	  	      EventBus.$emit('showMessage', mensaje);
	  	      _this.cleanForm();
	  	      _this.editing = false;
	  	    })
	  	    .fail(function(data) {
	  	      let mensaje = 'No se pudo actualizar el Perfil. Revise su conexión a Internet.';
	  	      EventBus.$emit('showMessage', mensaje);
	  	    });
	  	},
	  	remove: function () {
          var _this = this;
          $.ajax(
            {
              url : httpURL + this.Perfil.Id,
              type: "DELETE",
              data: {Id: this.Perfil.Id}
            })
            .done(function(data) {
              EventBus.$emit('updateListPerfil');
              _this.cleanForm();
              let mensaje ='Perfil eliminado con éxito.';
              EventBus.$emit('showMessage', mensaje);
              _this.editing = false;
            })
            .fail(function(data) {
              let mensaje = 'No se pudo eliminar el Perfil. Revise su conexión a Internet.';
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
              _this.Perfil = data;
            })
            .fail(function(data) {
              let mensaje = 'No se pudo cargar el Perfil. Revise su conexión a Internet.';
              EventBus.$emit('showMessage', mensaje);
            });
          },
        },
        data: function () {
          return {
            editing: false,
            addingNew: false,
            Perfil: {
              Id: '',
              Nombre: '',
              Descripcion: '',
              Departamento: '',
              EdadMedia: '',
              Administrador: ''
            },
            PerfilCopia: {
              Id: '',
              Nombre: '',
              Descripcion: '',
              Departamento: '',
              EdadMedia: '',
              Administrador: ''
            }
          }
        },
        mounted: function() {
          EventBus.$on('perfilSelected', function(id) {
            this.load(id);
            this.editing = false;
            this.addingNew = false;
          }.bind(this));
        }
	  
	}

</script>