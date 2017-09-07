<template>
  <div id="app">
    <master-chooser></master-chooser>
    <h2 v-show="opcion == 'perfiles'"><strong>{{ title.Perfiles }}</strong></h2>
    <maestro-perfil v-show="opcion == 'perfiles'"></maestro-perfil>
    <detalle-perfil v-show="opcion == 'perfiles'"></detalle-perfil>

    <h2 v-show="opcion == 'tareas'"><strong>{{ title.Tareas }}</strong></h2>
    <maestro-tarea v-show="opcion == 'tareas'"></maestro-tarea>
    <detalle-tarea v-show="opcion == 'tareas'"></detalle-tarea>

    <h2 v-show="opcion == 'campos'"><strong>{{ title.Campos }}</strong></h2>
    <maestro-campo v-show="opcion == 'campos'"></maestro-campo>
    <detalle-campo v-show="opcion == 'campos'"></detalle-campo>
    
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
import MaestroCampo from './components/Maestro-Campo.vue'
import DetalleCampo from './components/Detalle-Campo.vue'
import InfoMessage from './components/InfoMessage.vue'

export default {
  components: {
    'master-chooser' : Chooser,
    'maestro-perfil' : MaestroPerfil,
    'detalle-perfil' : DetallePerfil,
    'maestro-tarea' : MaestroTarea,
    'detalle-tarea' : DetalleTarea,
    'maestro-campo' : MaestroCampo,
    'detalle-campo' : DetalleCampo,
    'infomessage' : InfoMessage
  },

  data: function() {
    return {
      title: {
        Perfiles: 'Administrador de Perfiles',
        Tareas: 'Administrador de Tareas',
        Campos: 'Administrador de Campos'
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
