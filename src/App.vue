<template>
  <div id="app">
    <master-chooser></master-chooser>
    <h2 v-show="opcion == 'perfiles'"><strong>{{ title.Perfiles }}</strong></h2>
    <maestro-perfil v-show="opcion == 'perfiles'"></maestro-perfil>
    <detalle-perfil v-show="opcion == 'perfiles'"></detalle-perfil>
    
    <h2 v-show="opcion == 'tareas'"><strong>{{ title.Tareas }}</strong></h2>
    <maestro-tarea v-show="opcion == 'tareas'"></maestro-tarea>
    <detalle-tarea v-show="opcion == 'tareas'"></detalle-tarea>
    <infomessage style="clear:both"></infomessage>
  </div>
</template>

<script>
import EventBus from './components/event-bus.js'
import Chooser from './components/Chooser.vue'
import MaestroPerfil from './components/Maestro-perfil.vue'
import DetallePerfil from './components/Detalle-perfil.vue'
import MaestroTarea from './components/Maestro-Tarea.vue'
import DetalleTarea from './components/Detalle-Tarea.vue'
import InfoMessage from './components/InfoMessage.vue'

export default {
  components: {
    'master-chooser' : Chooser,
    'maestro-perfil' : MaestroPerfil,
    'detalle-perfil' : DetallePerfil,
    'maestro-tarea' : MaestroTarea,
    'detalle-tarea' : DetalleTarea,
    'infomessage' : InfoMessage
  },

  data: function() {
    return {
      title: {
        Perfiles: 'Maestro-Detalle de Perfil',
        Tareas: 'Maestro-Detalle de Tarea'
      },

      opcion: 'tareas'
    }
  },

  mounted: function(){
    EventBus.$on('chooseOption', function(opcion) {
      this.opcion = opcion;
    }.bind(this));
  }
}
</script>
