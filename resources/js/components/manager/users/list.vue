<template>
	<table class="table table-striped">
        <thead>
            <tr>
                <th>Họ và tên</th>
                <th>Tên đăng nhập</th>
                <th>Email</th>
                <th>Vị trí</th>
                <th>Hình ảnh</th>
                <th>Bị khoá</th>
            </tr>
        </thead>
        <tbody>
            <tr v-for="user in users" :key="user.id">
                <td>{{ user.name }}</td>
                <td>{{ user.username }}</td>
                <td>{{ user.email }}</td>
                <td>{{ user.type }}</td>
                <td><img :src='"/storage/profiles/" + user.photo_url' alt="imagem" height="120px" width="120px"></img></td>
                <td>
                    <template v-if="user.id !== $store.state.user.id">
                        <template v-if="user.blocked === 1">
                            <a class="fa fa-lock" v-on:click.prevent="unblock(user)"></a>
                        </template>  
                        <template v-else>
                            <a class="fa fa-unlock" v-on:click.prevent="block(user)"></a>
                        </template>
                    </template>
                </td>
                <td>
                    <template v-if="user.id !== $store.state.user.id">
	                   <a class="btn btn-sm btn-primary" v-on:click.prevent="editUser(user)">Sửa</a>
	                   <a class="btn btn-sm btn-danger" v-on:click.prevent="deleteUser(user)">Xoá</a>
                    </template>
        		</td>
            </tr>
            <a class="btn btn-sm btn-primary" v-on:click.prevent="addUser()">Thêm mới</a>
        </tbody>
    </table>
</template>

<script type="text/javascript">
	module.exports={
		props: ['users'],
		data: function(){
			return { 
				editingUser: null
			}
		},
        methods: {
            editUser: function(user){
                this.editingUser = user;
                this.$emit('edit-click', user);
            },		
            deleteUser: function(user){
                this.editingUser = user;
                this.$emit('delete-click', user);
			},
			addUser: function(){
				this.$emit('add-click');
			},
            unblock: function(user){
                this.$emit('unblock-user', user);
            },
            block: function(user){
                this.$emit('block-user', user);
            },
        },		
	}
</script>

<style scoped>

</style>