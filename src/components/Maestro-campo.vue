<template>
  <div style="min-width:300px; max-width:300px; display:inline-block; vertical-align:top">
    <ul class="w3-ul w3-card-4">
      <li><h2><strong>Campos</strong></h2></li>
      <li style="overflow: hidden; text-overflow: ellipsis" class="w3-hover-blue" v-for="Campo in Campos" @click="campoSelected(Campo.Id)"> Nombre: {{Campo.Nombre }}</li>
    </ul>
  </div>
</template>

<script>
import EventBus from './event-bus.js'
import AppIcon from './App-icon.vue'
import appConfig from './config.js'

var httpURL = appConfig.URLCampo;

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
          _this.Campos = data;
        })
        .fail(function(data) {
          let mensaje = 'No se pudo cargar la lista. Revise su conexión a Internet.';
          EventBus.$emit('showMessage', mensaje);
        });
      },

      campoSelected: function(id){
        EventBus.$emit('campoSelected', id);
      }
    },

    data: function () {
      return {
        Campos: [],
      }
    },

    mounted: function() {
      this.loadList();

      EventBus.$on('updateListCampo', function() {
        this.loadList();
      }.bind(this));
    }
  }

  </script>
