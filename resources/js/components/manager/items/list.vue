<template>
	<table class="table table-striped">
        <thead>
            <tr>
                <th>Tên</th>
                <th>Loại</th>
                <th>Thành phần</th>
                <th>Giá</th>
                <th>Hình ảnh</th>
            </tr>
        </thead>
        <tbody>
            <tr v-for="item in items" :key="item.id">
                <td>{{ item.name }}</td>
                <td>{{ item.type }}</td>
                <td>{{ item.description }}</td>
                <td>{{ item.price }}</td>
                <td><img :src='"/storage/items/" + item.photo_url' alt="imagem" height="120px" width="120px"></img></td>
                <td>
	                <a class="btn btn-sm btn-primary" v-on:click.prevent="editItem(item)">Sửa</a>
	                <a class="btn btn-sm btn-danger" v-on:click.prevent="deleteItem(item)">Xoá</a>
        		</td>
            </tr>
            <a class="btn btn-sm btn-primary" v-on:click.prevent="addItem()">Thêm mới</a>
        </tbody>
    </table>
</template>

<script type="text/javascript">
	module.exports={
		props: ['items'],
		data: function(){
			return { 
				editingItem: null
			}
		},
        methods: {
            editItem: function(item){
                this.editingItem = item; 
                this.$emit('edit-click', item);
            },		
            deleteItem: function(item){
                this.editingItem = item;
                this.$emit('delete-click', item);
			},
			addItem: function(){
				this.$emit('add-click');
			},
        },		
	}
</script>

<style scoped>

</style>