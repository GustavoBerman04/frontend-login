<template>
    <v-col col="12">
    <v-row class="renglon">
        <v-data-table
            :headers="headers"
            :items="usuarios"
            :items-per-page="5"
            class="elevation-1"
            style="width: 100%;"
        >
            <template #[`item.actions`]="{item}">
                <v-row class="renglon">
                    <v-col cols="6">
                        <v-btn icon color="orange" @click="dialogUpdate(item)"> 
                            <v-icon>mdi-human-edit</v-icon>
                        </v-btn>
                    </v-col>
                    <v-col cols="6">
                        <v-btn icon color="red" @click="dialogUser(item)">
                            <v-icon>mdi-eraser</v-icon>
                        </v-btn>
                    </v-col>
                </v-row>
            </template>
        </v-data-table>
    </v-row>
    <v-row class="renglon">
        <v-btn block color="blue" @click="open">
            Agregar Nuevo Usuario
        </v-btn>
    </v-row>
    <v-dialog v-model="openDialog" 
              width="800" 
              height="500"
              persistent>
        <v-card>
            <v-card-title>Datos del Usuario</v-card-title>
            <v-card-text>
                <v-form ref="formRegistro">
                    <v-text-field 
                    v-model="name"
                    type="text" 
                    placeholder="Name:" 
                    label="Name">
                    </v-text-field>
                    <v-text-field 
                    v-model="lastname"
                    type="text" 
                    placeholder="LastName:" 
                    label="LastName">
                    </v-text-field>
                    <v-text-field 
                    v-model="email"
                    type="email" 
                    placeholder="Email:" 
                    label="Email">
                    </v-text-field>
                    <v-text-field 
                    v-model="password"
                    type="password" 
                    placeholder="Password:" 
                    label="Password">
                    </v-text-field>
                    <v-text-field 
                    v-model="number"
                    type="text" 
                    placeholder="Number:" 
                    label="Number">
                    </v-text-field>
                </v-form>
            </v-card-text>
            <v-card-actions style="width:100%; display:flex; flex-direction:column;">
                <v-row style="width: 100%; margin-top: 5px; margin-bottom: 10px;">
                    <v-btn block color="green" @click="registrarUsuario">
                    Registrar
                    </v-btn>
                </v-row>
                <v-row style="width: 100%; margin-top: 5px; margin-bottom: 10px;">
                    <v-btn block color="red" @click="openDialog = false">
                    Cancelar
                    </v-btn>
                </v-row>
            </v-card-actions>
        </v-card>
    </v-dialog>
    <v-dialog v-model="openDialogErase" 
              width="500" 
              height="500"
              persistent>>
        <v-card>
            <v-card-title>Borrar Usuario</v-card-title>
            <v-card-text>??Realmente quieres borrar el usuario?</v-card-text>
            <v-card-actions>
                <v-btn color="red" @click="openDialogErase = false">
                    Cancelar
                </v-btn>
                
                <v-btn color="orange" @click="eraseUser">
                    Borrar
                </v-btn>
            </v-card-actions>
        </v-card>
    </v-dialog>
    <v-dialog v-model="openDialogUpdate" 
              width="800" 
              height="500"
              persistent>
        <v-card>
            <v-card-title>Datos del Usuario</v-card-title>
            <v-card-text>
                <v-form ref="formUpdate">
                    <v-text-field 
                    v-model="nameUpdate"
                    type="text" 
                    placeholder="Name:" 
                    label="Name">
                    </v-text-field>
                    <v-text-field 
                    v-model="lastnameUpdate"
                    type="text" 
                    placeholder="LastName:" 
                    label="LastName">
                    </v-text-field>
                    <v-text-field 
                    v-model="numberUpdate"
                    type="text" 
                    placeholder="Number:" 
                    label="Number">
                    </v-text-field>
                </v-form>
            </v-card-text>
            <v-card-actions style="width:100%; display:flex; flex-direction:column;">
                <v-row style="width: 100%; margin-top: 5px; margin-bottom: 10px;">
                    <v-btn block color="green" @click="actualizaUsuario">
                    Registrar
                    </v-btn>
                </v-row>
                <v-row style="width: 100%; margin-top: 5px; margin-bottom: 10px;">
                    <v-btn block color="red" @click="openDialogUpdate = false">
                    Cancelar
                    </v-btn>
                </v-row>
            </v-card-actions>
        </v-card>
    </v-dialog>
</v-col>
</template>

<script>
export default{
    data () {
        return {
            usuarios: [],
            headers: [
                {
                    text: 'Nombre',
                    align: 'center',
                    sortable: true,
                    value: 'name'
                },
                {
                    text: 'Apellidos',
                    align: 'center',
                    sortable: true,
                    value: 'lastname'
                },
                {
                    text: 'Correo Electronico',
                    align: 'center',
                    sortable: true,
                    value: 'email'
                },
                {
                    text: 'Numero',
                    align: 'center',
                    sortable: true,
                    value: 'number'
                },
                {
                    text: 'Acciones',
                    align: 'center',
                    sortable: true,
                    value: 'actions'
                }
            ],
            openDialog: false,
            name: '',
            lastname: '',
            email: '',
            password: '',
            emailEraseUser: '',
            openDialogErase: false,
            newemail: '',
            admin: 'AdminGustaboConB',
            openDialogUpdate: false,
            nameUpdate: '',
            lastnameUpdate: '',
            passwordUpdate: '',
            numberUpdate: '',
            datos: {}
        }
    },
    mounted () {
        this.loadUsers()
    },
    methods:{
        async loadUsers() {
            const config = {
                headers: {
                    'Content-Type': 'application/json;charset=UTF-8',
                    'Access-Control-Allow-Origin': '*'
                }
            }
            await this.$axios.get('/usuarios', config)
                .then((res) => {
                    //console.log('res', res)
                    if(res.data.message === 'Usuarios'){
                        this.usuarios = res.data.data
                    }
                })
                .catch((error) => {
                    console.log('error', error)
                })
        },
        open () {
            this.openDialog = true
        },
        async registrarUsuario() {
            const config = {
                headers: {
                    'Content-Type': 'application/json;charset=UTF-8',
                    'Access-Control-Allow-Origin': '*'
                }
            }
            const usuarioNuevo = {
                name: this.name,
                lastname: this.lastname,
                email: this.email,
                password: this.password,
                number: this.number
            }
            await this.$axios.post('/registro', usuarioNuevo, config)
                .then((res) => {
                    console.log('res',res)
                    if(res.data.alert === 'success'){
                        this.openDialog = false
                        this.loadUsers()
                    }
                })
                .catch((error) => {
                    console.log('error', error)
                })
        },
        async eraseUser() {
            if(this.admin !== 'AdminGustaboConB'){
                const config = {
                headers: {
                    'Content-Type': 'application/json;charset=UTF-8',
                    'Access-Control-Allow-Origin': '*'
                }
            }
                const usuario = {
                email: this.emailEraseUser
            }
                await this.$axios.post('/delete', usuario, config)
                    .then((res) => {
                    console.log(res)
                    if(res.data.alert === 'success'){
                        this.loadUsers()
                        this.openDialogErase = false
                    }
                    })
                    .catch((error) => {
                        console.log(error)
                    })
            }  
        },
        dialogUser(item) {
            this.emailEraseUser = item.email
            this.admin = item.name
            this.openDialogErase = true
        },
        dialogUpdate (item) {
            this.datos = item
            this.nameUpdate = this.datos.name
            this.lastnameUpdate = this.datos.lastname
            this.passwordUpdate = this.datos.password

            this.openDialogUpdate = true
        },

        async actualizaUsuario(){
            const config = {
                headers: {
                    'Content-Type': 'application/json;charset=UTF-8',
                    'Access-Control-Allow-Origin': '*'
                }
            }
                const usuarioNuevo = {
                email: this.datos.email,
                name: this.nameUpdate,
                lastname: this.lastnameUpdate,
                number: this.numberUpdate
            }
                await this.$axios.post('/update', usuarioNuevo, config)
                    .then((res) => {
                    console.log(res)
                    if(res.data.alert === 'success'){
                        this.loadUsers()
                        this.openDialogUpdate = false
                    }
                    })
                    .catch((error) => {
                        console.log(error)
                    })
        }
    }
}
</script>

<style scoped>
.renglon{
    width:100%;
    margin-top: 20px;
    margin-bottom: 20px;
}
</style>