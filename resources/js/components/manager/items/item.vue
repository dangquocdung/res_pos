<template>
    <div>
        <div class="box">
            <div class="box-header with-border">
                <h3 class="box-title">Món</h3>
            </div>
            <div class="box-body table-responsive">
                <div class="panel panel-info">
                    <div class="panel-body table-responsive">
                        <!--TABLE LIST-->
                        <item-list :items="items" @edit-click="editItem" @delete-click="deleteItem" @add-click="addingItem" ref="itemsListRef"></item-list>

                        <paginator :data="paginatorData" @change-page="getItems"></paginator>

                        <!--TABLE ADD-->    
                        <item-add @insert-error="insertError" @item-canceled="addItemCancel" @item-inserted="addItem" v-if="adding"></item-add>


                        <!--TABLE EDIT-->
                        <item-edit @edit-error="editError" :item="currentItem" @item-saved="savedItem" @item-canceled="cancelEdit" v-if="currentItem"></item-edit>

                    </div>
                </div>
            </div>
        </div>
            <!-- /.box-body -->
        <div class="box-footer">
            <!-- Footer -->
        </div>
        <!-- /.box-footer-->
    </div>
</template>

<script type="text/javascript">
    import ItemEdit from './edit.vue';
    import ItemList from './list.vue';
    import ItemAdd from './add.vue';
    import Paginator from './../../paginator.vue';
    
    export default {
        data: function(){
            return { 
                currentItem: null,
                adding: false,
                items: [],
                paginatorData: null,
            }
        },
        methods: {
            insertError: function() {
                this.$swal({
                    type: 'error',
                    text: 'Ôi không! Chúng tôi nghi ngờ có những thông tin bị thiếu.'
                });
            },
            editError: function() {
                this.$swal({
                    type: 'error',
                    text: 'Ôi không! Có lẽ tên món đã được đặt.'
                });
            },
            editItem: function(item){
                this.currentItem = Object.assign({},item);
            },

            deleteItem: function(item){
                this.$http.delete('api/items/'+item.id)
                    .then(response => {
                        this.$swal({
                            type: 'success',
                            title: 'Thành công',
                            text: 'Món đã bị xoá!'
                        });
                        this.getItems();
                    })
                    .catch(error => {
                        this.$swal({
                            type: 'error',
                            text: "Ồ không, chúng tôi hy vọng nó sẽ không xảy ra lần nữa!"
                        });
                    });
            },
            addingItem: function(){
                this.adding = true;
            },
            addItem: function(){
                this.$swal({
                    type: 'success',
                    title: 'Thành công',
                    text: 'Thêm món thành công!'
                });
                this.getItems();
            },
            addItemCancel: function(){
                this.adding = false;
            },
            savedItem: function(){
                this.currentItem = null;
                this.$refs.itemsListRef.editingItem = null;
                this.$swal({
                    type: 'success',
                    title: 'Thành công',
                    text: 'Cập nhật món thành công!'
                });
                this.getItems();
            },
            cancelEdit: function(){
                this.currentItem = null;
                this.$refs.itemsListRef.editingItem = null;
            },
            getItems: function(page){
                if(page == null){
                    page = 1
                }
                this.$http.get('api/items?page='+page)
                    .then(response => {
                        this.items = response.data.data; 
                        this.paginatorData = response.data;
                    }).catch(error => {
                        this.$swal({
                            type: 'error',
                            text: "Ồ không, nhận các mục từ DB không hoạt động!"
                        });
                    });
            }
        },
        components: {
            'item-list': ItemList,
            'item-edit': ItemEdit,
            'item-add': ItemAdd,
            'paginator': Paginator
        },
        mounted() {
            this.getItems();
        }

    }
</script>

<style scoped>  

</style>
