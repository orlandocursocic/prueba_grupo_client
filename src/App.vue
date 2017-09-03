<template>
  <div id="app">
    <master-chooser></master-chooser>
    <!--<h2 v-show="opcion == 'entradas'"><strong>{{ title.Entradas }}</strong></h2>
    <maestro-entrada v-show="opcion == 'entradas'"></maestro-entrada>
    <detalle-entrada v-show="opcion == 'entradas'"></detalle-entrada>-->
    <h2 v-show="opcion == 'tareas'"><strong>{{ title.Tareas }}</strong></h2>
    <maestro-tarea v-show="opcion == 'tareas'"></maestro-tarea>
    <!--<detalle-tarea v-show="opcion == 'tareas'"></detalle-pelicula>
    <infomessage style="clear:both"></infomessage>-->
  </div>
</template>

<script>
import EventBus from './components/event-bus.js'
import Chooser from './components/Chooser.vue'
/*import MaestroEntrada from './components/Maestro-Entrada.vue'
import DetalleEntrada from './components/Detalle-Entrada.vue'*/
import MaestroTarea from './components/Maestro-Tarea.vue'
import DetalleTarea from './components/Detalle-Tarea.vue'
//import InfoMessage from './components/InfoMessage.vue'

export default {
  components: {
    'master-chooser' : Chooser,
    /*'maestro-entrada' : MaestroEntrada,
    'detalle-entrada' : DetalleEntrada,*/
    'maestro-tarea' : MaestroTarea,
    'detalle-tarea' : DetalleTarea,
    //'infomessage' : InfoMessage
  },

  data: function() {
    return {
      title: {
        //Entradas: 'Maestro-Detalle de Entrada',
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
