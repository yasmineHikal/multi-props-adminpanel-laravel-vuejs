<template>
    <div class="container">
        <div class="row mt-5">
            <div class="col-md-12">
                <div class="card">
                    <div class="card-header">
                        <h3 class="card-title">Users List</h3>

                        <div class="card-tools">
                            <button class="btn btn-success" @click="newModel()">Add New
                                <i class="fa fa-user-plus"></i>

                            </button>

                        </div>
                    </div>
                    <!-- /.card-header -->
                    <div class="card-body table-responsive p-0">
                        <table class="table table-hover">
                            <thead>
                            <tr>
                                <th>ID</th>
                                <th>Name</th>
                                <th>Email</th>
                                <th>Type</th>
                                <th>created At</th>

                                <th>Modify</th>
                            </tr>
                            </thead>
                            <tbody>
                            <tr v-for="user in users" :key="user.id">
                                <td>{{user.id}}</td>
                                <td>{{user.name}}</td>
                                <td>{{user.email}}</td>
                                <td>{{user.type}}</td>
                                <td>{{user.created_at |myDate}}</td>

                                <td>
                                    <a href="#" @click="editModel(user)">
                                        <i class="fa fa-edit"></i>
                                    </a>
                                        /
                                     <a href="#" @click="deleteUser(user.id)">
                                        <i class="fa fa-trash"></i>
                                    </a>




                                </td>
                            </tr>

                            </tbody>
                        </table>
                    </div>
                    <!-- /.card-body -->
                </div>
                <!-- /.card -->
            </div>
        </div>
        <!-- Modal -->
        <div class="modal fade" id="AddNewUser" tabindex="-1" role="dialog" aria-labelledby="exampleModalCenterTitle" aria-hidden="true">
            <div class="modal-dialog modal-dialog-centered" role="document">
                <div class="modal-content">
                    <div class="modal-header">
                        <h5 class="modal-title" id="exampleModalCenterTitle2" v-show="!editMode">Add New</h5>
                        <h5 class="modal-title" id="exampleModalCenterTitle" v-show="editMode">Update User's Data</h5>

                        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                            <span aria-hidden="true">&times;</span>
                        </button>
                    </div>

                    <form @submit.prevent="editMode ? updateUser() : createUser()">
                    <div class="modal-body">
                        <div class="form-group">
                            <input v-model="form.name" type="text" name="name"
                                   placeholder="Name"
                                   class="form-control" :class="{ 'is-invalid': form.errors.has('name') }">
                            <has-error :form="form" field="name"></has-error>
                        </div>
                        <div class="form-group">
                            <input v-model="form.email" type="email" name="email"
                                   placeholder="Email Address"
                                   class="form-control" :class="{ 'is-invalid': form.errors.has('email') }">
                            <has-error :form="form" field="email"></has-error>
                        </div>

                        <div class="form-group">
                        <textarea v-model="form.bio" name="bio" id="bio"
                                  placeholder="Short bio for user (Optional)"
                                  class="form-control" :class="{ 'is-invalid': form.errors.has('bio') }"></textarea>
                            <has-error :form="form" field="bio"></has-error>
                        </div>


                        <div class="form-group">
                            <select name="type" v-model="form.type" id="type" class="form-control" :class="{ 'is-invalid': form.errors.has('type') }">
                                <option value="">Select User Role</option>
                                <option value="admin">Admin</option>
                                <option value="user">Standard User</option>
                                <option value="author">Author</option>
                            </select>
                            <has-error :form="form" field="type"></has-error>
                        </div>

                        <div class="form-group">
                            <input v-model="form.password" type="password" name="password" id="password"
                                   class="form-control" :class="{ 'is-invalid': form.errors.has('password') }">
                            <has-error :form="form" field="password"></has-error>
                        </div>



                    </div>
                        <div class="modal-footer">
                            <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
                            <button type="submit" v-show="!editMode" class="btn btn-primary">Create</button>
                            <button type="submit" v-show="editMode" class="btn btn-success">Update User</button>

                        </div>
                    </form>

                </div>
            </div>
        </div>
    </div>
</template>




<script>
    export default {
        data(){
            return{
                editMode : true ,
                users: {},
                form: new Form({
                    id : '' ,
                    name :'',
                    email:'',
                    password:'',
                    type:'',
                    bio:'',
                    photo:''
                })
            }
        },

        methods:{
            updateUser(){
                this.$Progress.start();
                this.form.put('api/user/'+this.form.id)
                    .then(() => {
                        Fire.$emit('afterCreate');
                        $('#AddNewUser').modal('hide')
                        this.$Progress.finish();
                        Swal.fire(
                            'Updated!',
                            'User data has been Updated.',
                            'success'
                        )
                    }).catch(()=>{
                    this.$Progress.fail();
                    Swal.fire({
                        icon: 'error',
                        title: 'Oops...',
                        text: 'Something went wrong!',
                    })
                })

            },
            editModel(user){
                this.editMode = true ;
                this.form.reset();
                $('#AddNewUser').modal('show')
                this.form.fill(user);
            },
            newModel(){
                this.editMode = false ;
                this.form.reset();
                $('#AddNewUser').modal('show')
            },
            deleteUser(id){
                Swal.fire({
                    title: 'Are you sure?',
                    text: "You won't be able to revert this!",
                    icon: 'warning',
                    showCancelButton: true,
                    confirmButtonColor: '#3085d6',
                    cancelButtonColor: '#d33',
                    confirmButtonText: 'Yes, delete it!'
                }).then((result) => {
                    if (result.value) {
                    //send request
                    this.form.delete('api/user/'+id).then(()=>{
                        Swal.fire(
                            'Deleted!',
                            'Your file has been deleted.',
                            'success'
                        )
                    }).catch(()=>{
                        Swal.fire({
                            icon: 'error',
                            title: 'Oops...',
                            text: 'Something went wrong!',
                        })
                    })

                        Fire.$emit('afterCreate');

                    }
                })
            },
            loadUsers(){
                axios.get('api/user').then(({data})=>{this.users = data.data});
            },
            createUser(){
                this.$Progress.start();
                this.form.post('api/user')
                    .then( () => {
                        Fire.$emit('afterCreate');
                        $('#AddNewUser').modal('hide')
                        this.$Progress.finish();
                        Toast.fire({
                            icon: 'success',
                            title: 'User created successfully'
                        })}
                      )
                    .catch(()=>{

                    });

            },


        },
        created() {
           this.loadUsers()
            //setInterval(()=>this.loadUsers() ,3000);
            Fire.$on('afterCreate',()=>{
                this.loadUsers();
            });
        },

    }
</script>
