<template>
    <div>
        <div class="row">
            <div class="col-12 m-4 d-flex justify-content-between p-3">
                <h2>Usuarios</h2>
                <!-- Button trigger modal -->
                <button
                    type="button"
                    class="btn btn-primary"
                    data-toggle="modal"
                    data-target="#addUserModal"
                >
                    Nuevo Usuario
                </button>
            </div>
            <div class="col-12 m-2">
                <input
                     v-model="search"
                     v-on:keyup="searchUser"
                    type="text"
                    class="form-control"
                    placeholder="Filtrar usuarios"
                />
            </div>
            <div class="col-md-3" v-for="user in users" :key="user.id">
                <div class="card m-2 ">
                    <div class="card-header d-flex justify-content-between">
                        <label for="">{{ user.name }}</label>
                        <label for=""
                            ><small>{{ user.created_at }}</small></label
                        >
                    </div>

                    <div class="card-body">{{ user.email }}</div>
                    <div class="car-footer d-flex justify-content-between p-3">
                        <button
                            class="btn btn-info"
                            data-toggle="modal"
                            data-target="#editUserModal"
                            v-on:click="showUser(user)"
                        >
                            Editar
                        </button>
                        <button
                            class="btn btn-danger"
                            v-on:click="deleteUser(user)"
                        >
                            Eliminar
                        </button>
                    </div>
                </div>
            </div>
        </div>

        <!-- Modal  add user-->

            <form v-on:submit.prevent="addUser"
                class="modal fade"
                id="addUserModal"
                tabindex="-1"
                role="dialog"
                aria-labelledby="exampleModalLabel"
                aria-hidden="true"
            >
                <div class="modal-dialog" role="document">
                    <div class="modal-content">
                        <div class="modal-header">
                            <h5 class="modal-title" id="exampleModalLabel">
                                Nuevo Usuario
                            </h5>
                            <button
                                type="button"
                                class="close"
                                data-dismiss="modal"
                                aria-label="Close"
                            >
                                <span aria-hidden="true">&times;</span>
                            </button>
                        </div>
                        <div class="modal-body">
                            <form>
                                <div class="form-group">
                                    <label
                                        for="recipient-name"
                                        class="col-form-label"
                                        >Nombre</label
                                    >
                                    <input
                                        type="text"
                                        class="form-control"
                                        v-model="newUser.name"
                                    />
                                </div>
                                <div class="form-group">
                                    <label
                                        for="recipient-name"
                                        class="col-form-label"
                                        >Correo</label
                                    >
                                    <input
                                        type="email"
                                        class="form-control"
                                        v-model="newUser.email"
                                    />
                                </div>
                            </form>
                        </div>
                        <div class="modal-footer">
                            <button
                                type="button"
                                class="btn btn-secondary"
                                data-dismiss="modal"
                            >
                                Close
                            </button>
                            <button type="submit" class="btn btn-success">
                                Guardar
                            </button>
                        </div>
                    </div>
                </div>

        </form>

            <form v-on:submit.prevent="updateUser"
                class="modal fade"
                id="editUserModal"
                tabindex="-1"
                role="dialog"
                aria-labelledby="exampleModalLabel"
                aria-hidden="true"
            >
                <div class="modal-dialog" role="document">
                    <div class="modal-content">
                        <div class="modal-header">
                            <h5 class="modal-title" id="exampleModalLabel">
                                Editar usuario {{editUser.id}}
                            </h5>
                            <button
                                type="button"
                                class="close"
                                data-dismiss="modal"
                                aria-label="Close"
                            >
                                <span aria-hidden="true">&times;</span>
                            </button>
                        </div>
                        <div class="modal-body">
                             <input
                                    type="hidden"
                                    class="form-control"
                                    v-model="editUser.id"
                                />
                            <div class="form-group">

                                <label
                                    for="recipient-name"
                                    class="col-form-label"
                                    >Nombre</label
                                >
                                <input
                                    type="text"
                                    class="form-control"
                                    v-model="editUser.name"
                                />
                            </div>
                            <div class="form-group">
                                <label
                                    for="recipient-name"
                                    class="col-form-label"
                                    >Correo</label
                                >
                                <input
                                    type="email"
                                    class="form-control"
                                    v-model="editUser.email"
                                />
                            </div>
                        </div>
                        <div class="modal-footer">
                            <button
                                type="button"
                                class="btn btn-secondary"
                                data-dismiss="modal"
                            >
                                Close
                            </button>
                            <button type="submit" class="btn btn-success">
                                Guardar
                            </button>
                        </div>
                    </div>
                </div>
            </form>

    </div>
</template>

<script>
export default {
    data() {
        return {
            users: [],
            newUser: [],
            editUser: {
               name:'',
                email:'',
                id:''
            },
            search:""
        };
    },
    created() {
        this.getUsers();
    },
    methods: {
        getUsers() {
            axios.get("http://laravel-vue.test/api/user").then(result => {
                return (this.users = result.data);
            });
        },
       searchUser() {
           if(this.search==""){
               return this.getUsers();
           }
            axios.get("http://laravel-vue.test/api/searchUser/"+this.search).then(result => {
                return (this.users = result.data);
            });
        },
          showUser(user) {
            axios.get("http://laravel-vue.test/api/user/"+user.id).then(result => {

                this.editUser.name=result.data.name;
                this.editUser.email=result.data.email;
                this.editUser.id=result.data.id;

            });
        },

        addUser() {
            axios
                .post("http://laravel-vue.test/api/user", {
                    name: this.newUser.name,
                    email: this.newUser.email
                })
                .then(result => {
                    alert("insertado");
                    this.users.push(this.newUser);
                    this.newUser = {};
                });
        },
        updateUser() {
            axios

                .put("http://laravel-vue.test/api/user/"+this.editUser.id, {
                    name: this.editUser.name,
                    email: this.editUser.email
                })
                .then(result => {
                    alert("editado");
                    this.getUsers();
                });
        },
        deleteUser(data) {
            if (confirm("¿estas seguro de eliminar el registro?")) {
                axios
                    .delete("http://laravel-vue.test/api/user/" + data.id)
                    .then(result => {
                        this.users.splice(this.users.indexOf(data.id), 1);
                    });
            }
        }
    },
    mounted() {}
};
</script>
