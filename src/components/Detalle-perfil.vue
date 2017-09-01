<template>
	<div>
		<div>
		  <h3 style="overflow: hidden; text-overflow: ellipsis; max-width:400px"><strong>Perfil: </strong>{{Perfil.Nombre}}</h3>
		  <label class="w3-text" for="nombre"> Nombre </label>
		  <input class="w3-input w3-border" style="overflow: hidden; text-overflow: ellipsis" type="string" name="nombre" value="Nombre" :disabled="!editing && !addingNew" v-model="Perfil.Nombre">
		  <label class="w3-text" for="descripcion"> Descripción </label>
		  <input class="w3-input w3-border" style="overflow: hidden; text-overflow: ellipsis" type="string" name="descripcion" value="Descripción" :disabled="!editing && !addingNew" v-model="Perfil.Descripción">
		  <label class="w3-text" for="departamento"> Departamento </label>
		  <input class="w3-input w3-border" style="overflow: hidden; text-overflow: ellipsis" type="string" name="departamento" value="Departamento" :disabled="!editing && !addingNew" v-model="Perfil.Descripcion">
		  <label class="w3-text" for="edad-media"> Edad Media </label>
		  <input class="w3-input w3-border" style="overflow: hidden; text-overflow: ellipsis" type="numeric" name="edad-media" value="Edad-Media" :disabled="!editing && !addingNew" v-model="Perfil.Edad-Media"><

		  <!--  falta agregar campo de Administrador  -->
		</div>









	</div>
</template>>