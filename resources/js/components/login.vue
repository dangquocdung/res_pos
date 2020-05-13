<template>
    <div class="box box-default">
        <div class="box-header with-border">
            <h3 class="box-title">Đăng nhập</h3>
        </div>
        <!-- /.box-header -->
        <div class="box-body table-responsive">
            <div class="form-group">
                <label for="inputEmail">Email hoặc tên người dùng</label>
                <input
                    type="email" class="form-control" v-model.trim="user.email"
                    @keyup.13="focusPwd" name="email" id="inputEmail"
                    placeholder="Email address"/>
            </div>
            <div class="form-group">
                <label for="inputPassword">Mật khẩu</label>
                <input
                    type="password" class="form-control" v-model="user.password" ref="passwordBox"
                    @keyup.13="login" name="password" id="inputPassword"
                    placeholder="Password"/>
            </div>
            <div class="form-group">
                <button class="btn btn-primary" @click="login">Đăng nhập</button>
            </div>
        </div>
        <!-- /.box-body -->
    </div>
</template>

<script type="text/javascript">    
    export default {
        data: function(){
            return { 
                user: {
                    email: "",
                    password: ""
                }
            }
        },
        methods: {
            focusPwd: function() {
                this.$refs.passwordBox.focus();
            },
            login: function() {
                if (this.user.email === "" || this.user.password === "") {
                    /////////////////////////////////////////
                    // SweetAlert
                    this.$swal({
                        type: 'info',
                        title: "Không thể đăng nhập",
                        text: 'Bạn phải điền đầy đủ thông tin'
                    });

                    return;
                    /////////////////////////////////////////
                }

                /////////////////////////////////////////
                // SweetAlert
                const toast = this.$swal.mixin({
                    toast: true,
                    position: 'top',
                    showConfirmButton: false
                });
                toast({
                    title: 'Đang đăng nhập...',
                    onBeforeOpen: () => {
                        this.$swal.showLoading();
                    }
                });
                /////////////////////////////////////////

                this.$http.post('api/login', this.user)
                    .then(response => {
                        this.$store.commit('setToken',response.data.access_token);
                        return this.$http.get('api/users/me');
                    })
                    .then(response => {
                        this.$store.commit('setUser', response.data.data);

                        if (this.$store.state.user.shift_active) {
                            this.$root.loadActiveData();
                        }

                        this.$router.push("itemsMenu");

                        /////////////////////////////////////////
                        // SweetAlert
                        const toast = this.$swal.mixin({
                            toast: true,
                            position: 'top',
                            showConfirmButton: false,
                            timer: 3000
                        });
                        toast({
                            type: 'success',
                            title: 'Đã đăng nhập thành công'
                        });
                        /////////////////////////////////////////
                    })
                    .catch(error => {
                        this.$store.commit('clearUserAndToken');
                        console.log(error);

                        /////////////////////////////////////////
                        // SweetAlert
                        this.$swal({
                            type: 'error',
                            title: 'Đăng nhập thất bại',
                            text: 'Thông tin không hợp lệ!'
                        });
                        /////////////////////////////////////////
                    });
            }
        },
    }
</script>
