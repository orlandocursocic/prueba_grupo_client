<template>
  <div style="min-width:400px; max-width:400px; display:inline-block; vertical-align:top">
    <ul class="w3-ul w3-card-4">
      <li><h2><strong>Perfiles de Usuarios</strong></h2></li>
      <li style="overflow: hidden; text-overflow: ellipsis" class="w3-hover-blue" v-for="Perfil in Perfiles" @click="perfilSelected(Perfil.Id)"> Perfil: {{Perfil.Nombre}}</li>
    </ul>
  </div>
</template>

<script>

import EventBus from './event-bus.js'
import AppIcon from './App-icon.vue'
import appConfig from './config.js'

var httpURL = appConfig.URLPerfil;

export default {
  components: {
    'app-icon' : AppIcon
  },
  methods: {
  	loadList: function(){
      var _this = this;
      $.ajax(
        {
          url : httpURL,
          type: "GET",
        })
        .done(function(data) {
          _this.Perfiles = data;
        })
        .fail(function(data) {
          let mensaje = 'No se pudo cargar la lista. Revise su conexión a Internet.';
          EventBus.$emit('showMessage', mensaje);
        });
    },

    perfilSelected: function(id){
    	EventBus.$emit('perfilSelected', id);
    }

  },

  data: function () {
	  return {
	    Perfiles: [],
	  }
  },

  mounted: function() {
      this.loadList();

	  EventBus.$on('updateListPerfil', function() {
	    this.loadList();
	  }.bind(this));
  }

}

</script>