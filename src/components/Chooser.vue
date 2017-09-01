<template>
  <div>
    <!--<button type="button" class="btn btn-default btn-sm" title="Entrada" @click="chooseOption(options.Entradas)">
      <app-icon img="th-list"></app-icon> Entradas
    </button>-->
    <button type="button" class="btn btn-default btn-sm" title="Tarea" @click="chooseOption(options.Tareas)">
      <app-icon img="th-list"></app-icon> Tareas
    </button>
  </div>
</template>

<script>
import EventBus from './event-bus.js'
//import AppIcon from './App-icon.vue'

export default {
  components: {
    //'app-icon' : AppIcon
  },

  data: function() {
    return {
      options: {
       // Entradas: 'entradas',
        Tareas: 'tareas'
      }
    }
  },
  methods: {
    chooseOption: function(opcion){
      EventBus.$emit('chooseOption', opcion);
    }
  }
}

</script>