<template>
    <div class="container">
        <div class="row mt-5">
          <div class="col-md-12">
            <div class="card">
              <div class="card-header">
                <h3 class="card-title">User Table</h3>

                <div class="card-tools">
                    <button class="btn btn-success" data-toggle="modal" data-target="#addNew" >Add User<i class="fas fa-user-plus fa-fw"></i></button>
                </div>
            </div>
            <!-- /.card-header -->
            <div class="card-body table-responsive p-0">
                <table class="table table-hover">
                  <tbody><tr>
                    <th>ID</th>
                    <th>Name</th>
                    <th>Email</th>
                    <th>Type</th>
                    <th>Register At</th>
                    <th>Bio</th>

                </tr>
                <tr v-for="user in users" :key="user.id">
                    <td>{{user.id}}</td>
                    <td>{{user.name}}</td>
                    <td>{{user.email}}</td>
                    <td>{{user.type |upText}}</td>
                    <td>{{user.created_at |myDate}}</td>
                    <td>{{user.bio}}</td>

                    <td>
                        <a href="#">
                            <i class="fa fa-edit"></i>
                        </a>
                        /
                        <a href="#" @click="deleteUser(user_id)">
                            <i class="fa fa-trash text-red"></i>
                        </a>
                    </td>
                </tr>

            </tbody></table>
        </div>
        <!-- /.card-body -->
    </div>
    <!-- /.card -->
</div>
</div>
<!-- Modal -->
<div class="modal fade" id="addNew" tabindex="-1" role="dialog" aria-labelledby="exampleModalLabel" aria-hidden="true">
  <div class="modal-dialog modal-dialog-centered" role="document">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title" id="addNew">Add New User</h5>
        <button type="button" class="close" data-dismiss="modal" aria-label="Close">
          <span aria-hidden="true">&times;</span>
      </button>
  </div>

  <form @submit.prevent="createUser">
      <div class="modal-body" >

        <div class="form-group">
          <input type="text" name="name" v-model="form.name" 
          placeholder="Name" 
          class="form-control" :class="{ 'is-invalid': form.errors.has('name') }">
          <has-error :form="form" field="name"></has-error>
      </div>


      <div class="form-group">
          <input type="text" name="email" id="email" v-model="form.email"
          placeholder="Email" 
          class="form-control" :class="{ 'is-invalid': form.errors.has('email') }">
          <has-error :form="form" field="email"></has-error>
      </div>



      <div class="form-group">
          <input type="password" id="password" name="password" v-model="form.password"
          placeholder="password" 
          class="form-control" :class="{ 'is-invalid': form.errors.has('password') }">
          <has-error :form="form" field="password"></has-error>
      </div>


      <div class="form-group">
          <select name="type" id="type" v-model="form.type"
          class="form-control" :class="{ 'is-invalid': form.errors.has('type') }">

          <option value="">Selecte User Role</option>
          <option value="admin">Admin User</option>
          <option value="user">User</option>
          <option value="owner">Owner</option>
      </select> 
      <has-error :form="form" field="type"></has-error>
  </div>


  <div class="form-group">
    <textarea v-model="form.bio" name="bio" id="bio" placeholder="Short Bio for use (opinal)" class="form-control" :class="{ 'is-invalid': form.errors.has('bio') }">
    </textarea>
    <has-error :form="form" field="Email"></has-error>

</div>

</div>

<div class="modal-footer">
    <button type="button" class="btn btn-secondary btn-danger" data-dismiss="modal">Close</button>
    <button type="submit" class="btn btn-primary">Create</button>

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
            return {


                users :{},
                form: new Form({
                    name : '',
                    email: '',
                    password: '',
                    type : '',
                    bio : '',
                    photo : ''   
                })
            }
        },

        methods: {


            deleteUser(id){
             swal({
                  title: 'Are you sure?',
                  text: "You won't be able to revert this!",
                  type: 'warning',
                  showCancelButton: true,
                  confirmButtonColor: '#3085d6',
                  cancelButtonColor: '#d33',
                  confirmButtonText: 'Yes, delete it!'
                }).then((result) => {
                 
                    this.form.delete('api/user/' +id).then(() =>{
                      
                        if (result.value) {
                            swal(
                              'Deleted!',
                              'Your file has been deleted.',
                              'success'
                              )
                        }      
                    }).catch(() =>{
                        swal("Failed","There was something wrong","warning");
                    });
                  
                })
            },

            loadUsers(){

                axios.get("api/user").then(({data})=>(this.users=data.data));
            },

            createUser(){
                this.$Progress.start();
                this.form.post('/api/user')
               
                .then(() =>{
                    Fire.$emit('AfterCreate');

                    $('#addNew').modal('hide');
                    toast({
                      type: 'success',
                      title: 'User Created successfully'
                  })
                    this.$Progress.finish();

                })

                .catch(() =>{

                })

            }
        },
        mounted() {
            this.loadUsers();
            Fire.$on('AfterCreate',() => {
                this.loadUsers();
            });

            setInterval(()=>{this.loadUsers()},3000);
        }
    }
</script>
