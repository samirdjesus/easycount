<template>
    <div>
        <b-alert
            :show="dismissCountDown"
            dismissible
            :variant="mensaje.color"
            @dismissed="dismissCountDown=0"
            @dismiss-count-down="countDownChanged"
        >
{{mensaje.texto}}
</b-alert>
        <form @submit.prevent="agregarNota(nota)" v-if="agregar">
            <h3 class="text-center">Agregar nueva Nota</h3>
            <input type="text" placeholder="Ingrese un Nombre" class="formcontrol my-2" v-model="nota.nombre">
            <input type="text" placeholder="Ingrese una descripcion"
                class="form-control my-2" v-model="nota.descripcion">
            <b-button   b-button class="btn-sm btn-block btn-success"
                type="submit">Agregar</b-button>
        </form>









        <table class="table">
        <thead>
        <tr>
        <th scope="col">#</th>
        <th scope="col">Nombre</th>
        <th scope="col">Descripción</th>
        <th scope="col">Fecha</th>
        <th scope="col">Acciones</th>
        </tr>
        </thead>
        <tbody>
        <tr v-for="(item, index) in notas" :key="index">
        <th scope="row">{{item._id}}</th>
        <td>{{item.nombre}}</td>
        <td>{{item.descripcion}}</td>
        <td>{{item.date}}</td>
        <td>
        <b-button class="btn-warning btn-sm mx-2"
            @click="activarEdicion(item._id)">Actualizar</b-button>
        <b-button class="btn-danger btn-sm mx-2"
            @click="eliminarNota(item._id)">Eliminar</b-button>
        </td>
        </tr>
        </tbody>
        </table>








    </div>    

</template>
<script>
export default {
    data() {
        return {
            notas: [],
            mensaje: {color: 'success', texto: ''},
            dismissSecs: 5,
            dismissCountDown: 0,
            nota: {},
            agregar: true,
        };
    },
    created(){
        this.listarNotas();
    },
    methods:{
        countDownChanged(dismissCountDown) {
            this.dismissCountDown = dismissCountDown
        },
        showAlert() {
            this.dismissCountDown = this.dismissSecs
        },
        listarNotas(){
            this.axios.get('nota')
            .then((response) => {
            // console.log(response.data)
            this.notas = response.data;
            })
        .catch((e)=>{
        console.log('error' + e);
        })
        },
        agregarNota(item){
        this.axios.post('nueva-nota', item)
        .then(res => {
        // Agrega al inicio de nuestro array notas
            this.notas.unshift(res.data);
            // Alerta de mensaje
            this.showAlert();
            this.mensaje.texto = 'Notas Agregada!'
            this.mensaje.color = 'success';
        })
        .catch( e => {
            console.log(e.response.data.error.errors.nombre.message);
            // Alerta de mensaje
            this.showAlert();
            this.mensaje.color = 'danger';
            this.mensaje.texto =
            e.response.data.error.errors.nombre.message;
        })
        this.notas = {}
        },
    }
};

</script>

