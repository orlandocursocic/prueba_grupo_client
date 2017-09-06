<template>
  <div>
    <button type="button" class="btn btn-default btn-lg" title="Perfil" @click="chooseOption(options.Perfiles)">
      <app-icon img="th-list"></app-icon> Perfiles
    </button>
    <button type="button" class="btn btn-default btn-lg" title="Tarea" @click="chooseOption(options.Tareas)">
     <app-icon img="th-list"></app-icon> Tareas
    </button>
    <button type="button" class="btn btn-default btn-lg" title="Campo" @click="chooseOption(options.Campos)">
     <app-icon img="th-list"></app-icon> Campos
   </button>
  </div>
</template>

<script>
import EventBus from './event-bus.js'
import AppIcon from './App-icon.vue'

export default {
  components: {
    'app-icon' : AppIcon
  },

  data: function() {
    return {
      options: {
        Perfiles: 'perfiles',
        Tareas: 'tareas',
        Campos: 'campos'
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
