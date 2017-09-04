<template>
  <div style="min-width:300px; max-width:300px; display:inline-block; vertical-align:top">
    <ul class="w3-ul w3-card-4">
      <li><h2><strong>Tareas</strong></h2></li>
      <li style="overflow: hidden; text-overflow: ellipsis" class="w3-hover-blue" v-for="Tarea in Tareas" @click="tareaSelected(Tarea.Id)"> Nombre: {{Tarea.Nombre}}</li>
    </ul>
  </div>
</template>

<script>
import EventBus from './event-bus.js'
import AppIcon from './App-icon.vue'
import appConfig from './config.js'

var httpURL = appConfig.URLTarea;

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
          _this.Tareas = data;
        })
        .fail(function(data) {
          let mensaje = 'No se pudo cargar la lista. Revise su conexión a Internet.';
          EventBus.$emit('showMessage', mensaje);
        });
      },

      tareaSelected: function(id){
        EventBus.$emit('tareaSelected', id);
      }
    },

    data: function () {
      return {
        Tareas: [],
      }
    },

    mounted: function() {
      this.loadList();

      EventBus.$on('updateListTarea', function() {
        this.loadList();
      }.bind(this));
    }
  }

  </script>