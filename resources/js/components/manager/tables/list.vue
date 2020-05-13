<template>
    <table class="table table-striped">
        <thead>
	        <tr>
	            <th>Bàn</th>
	            <th>Thời gian tạo</th>
	            <th>Thời gian cập nhật</th>
	        </tr>
	    </thead>
	    <tbody>
	        <tr v-for="table in tables"  :key="table.table_number" :class="{activerow: editingTable === table}">
	            <td>{{ table.table_number }}</td>
	            <td>{{ table.created_at.date }}</td>
	            <td>{{ table.updated_at.date }}</td>
	            <td>
	                <a class="btn btn-sm btn-primary" v-on:click.prevent="editTable(table)">Sửa</a>
	                <a class="btn btn-sm btn-danger" v-on:click.prevent="deleteTable(table)">Xoá</a>
	        	</td>
	        </tr>
	        <a class="btn btn-sm btn-primary" v-on:click.prevent="addTable()">Thêm mới</a>
	    </tbody>
    </table>
</template>

<script type="text/javascript">
	module.exports={
		props: ['tables'],
		data: function(){
			return { 
				editingTable: null
			}
		},
        methods: {
            editTable: function(table){
                this.editingTable = table;
                this.$emit('edit-click', table);
            },		
            deleteTable: function(table){
                this.editingTable = table;
                this.$emit('delete-click', table);
			},
			addTable: function(){
				this.$emit('add-click');
			},
        },		
	}
</script>

<style scoped>

</style>