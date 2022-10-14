<template>
  <!-- <HelloWorld msg="Welcome to Your Vue.js App"/> -->

	<div class="limiter">
		<div class="container-login100">
			<div class="wrap-login100">
				<div class="login100-pic js-tilt" data-tilt>
					<img src="./assets/logo.png" alt="IMG">
				</div>
	
				<form class="login100-form validate-form">
					<span class="login100-form-title">
						Los números ganadores de la semana pasada
					</span>
	
					<div class="wrap-input100 validate-input" data-validate = "Este campo no puede estar vacio">
						<table v-if="CountSorteos > 0" class="table table-striped">
							<tr class="thead-dark">
								<th>Sorteo</th>
								<th>Inicio a jugar</th>
								<th>Se jugó</th>
								<th>Premio</th>
								<th>Boleto ganador</th>
							</tr>
							<tr v-for="element in Sorteos" :key="element.id">
                <td>{{element.numero}}</td>
                <td>{{fdate(element.ini)}}</td>
                <td>{{fdate(element.fin)}}</td>
                <td>$ {{formatPrice(element.valor_ganable)}}</td>
                <td> {{(element.codigo)}}</td>

							</tr>
						</table>
						<table v-else class="table table-striped">
              <tr>
                <th>No hay sorteos de la semana pasada</th>
              </tr>
						</table>
					</div>
				</form>
      <div class="txt2">
        <h5>↓↓Para participar, baje al siguiente formulario↓↓</h5>
      </div>
			</div>
		</div>
	</div>
	
	<div class="limiter">
		<div class="container-login1001">
				<form @submit.prevent="addBoleto()"    
            class="login1001-form validate-form">
					<span class="login1001-form-title">
						¡Participe aqui!
					</span>

          <p v-if="errors.length > 0">
            <b class="txt_error">Por favor, corrija los siguientes errores:</b>
            <ul>
              <li v-for="error in errors" :key="error.id"> <p class="txt_error">{{ error }}</p></li>
            </ul>
          </p>
          <p v-if="noerrors.length > 0">
            <b class="txt_felicidades">Felicidades:</b>
            <ul>
              <li v-for="message in noerrors" :key="message.id"> <p class="txt_felicidades">{{ message }}</p></li>
            </ul>
          </p>

					<div class="wrap-input100 validate-input" data-validate = "Este codigo ya fue escojido">
            <label for="codigo" class="txt3">Pruebe suerte con un número</label>
						<input v-model.number="codigo" required
                type="number" name="codigo" id="codigo" placeholder="Reserve su boleta"
                class="input100" 
              >
					</div>

					<div class="wrap-input100 validate-input" data-validate = "Nombre requerido">
            <label for="codigo_a_reservar" class="txt3">Nombre completo</label>
						<input v-model="nombre" required
                type="text" name="Nombre_completo" placeholder="Nombre Completo"
                class="input100">
					</div>
					<div class="wrap-input100 validate-input" data-validate = "Nombre requerido">
            <label for="SorteoSeleccionado" class="txt3">Numero del sorteo</label>
						<select v-model="SorteoSeleccionado" class="form-control" id="SorteoSeleccionado">
              <option value="0">Seleccione un sorteo...</option>
              <option v-for="sorte in SorteosDisponibles" :key="sorte.id" :value="sorte.id">{{sorte.numero}} - {{fdate(sorte.fin,1)}}</option>
            </select>
					</div>
          <div class="text-center pt-3">
						<span v-if="SorteoSeleccionado != 0" class="txt3">
               Esta boleta tiene un costo de ${{ formatPrice(costo) }} COP
						</span>
					</div>
					<div class="container-login100-form-btn">
						<button class="login100-form-btn">
							Reservar
						</button>
					</div>

					
				</form>
		</div>
	</div>


  <div class="limiter">
		<div class="container-login1001">
			<div class="wrap-login1001">
				<div class="login100-form validate-form">
					<span class="login100-form-title">
						Ultimas Boletas
					</span>
	
					<div class="wrap-input100 validate-input" data-validate = "Este campo no puede estar vacio">
						<table v-if="CountBoletas > 0" class="table table-striped">
							<tr class="thead-dark">
								<th>Código</th>
								<!-- <th>Costo</th> -->
								<th>Dueño</th>
								<th>Sorteo</th>
								<th>Premio</th>
								<th>Gano?</th>
							</tr>
							<tr v-for="element in UltimasBoletas" :key="element.id">
                <td>{{element.codigo}}</td>
                <!-- <td>{{element.costo}}</td> -->
                <td>{{element.Usuario_nombre}}</td>
                <td>{{element.Sorteo}}</td>
                <td>{{formatPrice(element.Premio)}}</td>
                <td>{{gano(element.ganadora)}}</td>

							</tr>
						</table>
						<table v-else class="table table-striped">
              <tr>
                <th>No hay boletas</th>
              </tr>
						</table>
					</div>
				</div>
			</div>
		</div>
	</div>

</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'
import axios from 'axios'
import moment from 'moment'


export default {
  name: 'App',
  components: {
  },
  data(){
    return{
      API_Boleto : 'http://localhost:8000/api/Boletas',
      Sorteos:null,
      idBoletos:null,
      CountSorteos:0,
      codigo:null,
      nombre:'',
      UltimasBoletas:null,
      CountBoletas:1,
      valueSorteo:null,
      SorteoSeleccionado:0,
      SorteosDisponibles:null,
      costo: 0,
      errors:[],
      noerrors:[]
    }
  },
  mounted(){
    this.getSorteos();
    this.getSorteosDisponibles();
    this.getBoletosIds();
    this.getBoletosUltimas();
    this.asignarCostoBoleta();
  },
  methods:{
    getSorteos(){
      axios.get('http://localhost:8000/api/Sorteo').then( response =>{
          this.Sorteos = response.data;
          this.CountSorteos = this.Sorteos.length;
        }).catch( e => console.log(e) );
    },
    getSorteosDisponibles(){
      axios.get('http://localhost:8000/api/Sorteo/1').then( response =>{
          this.SorteosDisponibles = response.data;
        }).catch( e => console.log(e) );
    },
    getBoletosIds(){
      axios.get(this.API_Boleto).then( response =>{
          this.idBoletos = response.data
        }).catch( e => console.log(e) );
    },
    getBoletosUltimas(){
      axios.get(this.API_Boleto+'/1').then( response =>{
          this.UltimasBoletas = response.data
        }).catch( e => console.log(e) );
    },
    asignarCostoBoleta(){
      let MesActual = parseInt(new Date().getMonth() + 1);
      this.costo = MesActual*1000;
    },

    fdate(value , diff = null) {
      if (value) {
        if (diff != null) {
          let difdias = (moment().diff(value, 'days'));
          if (difdias == 0) {
            return ' juega Hoy';
          } else {
            return ' juega en '+(difdias*(-1))+' días';
          }
        } else {
          return moment(String(value)).format('DD/MMMM/YYYY');
        }
      }
    },

    formatPrice(value) {
        let val = (value/1).toFixed(0).replace('.', ',');
        return val.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ".");
    },
    gano(val){
      if (val) {
        return "¡¡Gano!!🤑";
      }else{
        return 'Aun no juega';
      }
    },
    checkForm(){
      if(this.codigo 
        && this.nombre
        && this.SorteoSeleccionado
        ){
            //buscar codigo repetido
            let BreakException = {};//break artificial
            try {
              this.idBoletos.forEach(element => {
                let enteroElement = parseInt(element);
                if(enteroElement == this.codigo){
                  throw BreakException;
                }
              });
            } catch (e) {
              
              return false;
            }
        return true;
      }

      if(this.SorteoSeleccionado == 0){
        this.errors.push('Por favor, Seleccione un sorteo.');
      }
      if(!this.nombre){
        this.errors.push('El nombre es obligatorio.');
      }
      if (!this.codigo) {
        this.errors.push('El codigo es obligatorio.');
      }
      return false;
    },

    //ADD
    addBoleto(){
      if(this.checkForm()){
        // console.log(this.SorteoSeleccionado);
        axios.post(this.API_Boleto, {
          codigo:this.codigo,
          costo:this.costo,
          sorteo_id:this.SorteoSeleccionado,
          nombre:this.nombre
        }).then( (response) => {
            console.log(response);
            this.noerrors.push('la boleta #'+this.codigo+' ha sido asiganada a '+this.nombre);
            this.errors=[];
            this.codigo = '';
            this.nombre = '';
            this.SorteoSeleccionado = 0;
            this.getBoletosIds();
            this.getBoletosUltimas();
        });
      }else{
        this.errors.push('El codigo ya esta en juego.');
      }
    },
  }
}
</script>

<style>

</style>
