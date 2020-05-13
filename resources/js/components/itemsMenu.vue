<template>
    <div class="box box-info">
        <div class="box-header with-border">
            <h3 class="box-title">Thực đơn</h3>
        </div>
        <div class="box-body table-responsive">
            <!--MENU LIST-->
            <template v-if="items">
                <table class="table table-striped">
                    <thead>
                        <tr>
                            <th>Hình ảnh</th>
                            <th>Tên</th>
                            <th>Loại</th>
                            <th>Thành phần</th>
                            <th>Giá</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="item in items" :key="item.id">
                            <td>
                                <img :src='"/storage/items/" + item.photo_url'
                                     alt="imagem" height="120px" width="120px"/>
                            </td>
                            <td>{{ item.name }}</td>
                            <td>{{ item.type }}</td>
                            <td>{{ item.description }}</td>
                            <td>{{ item.price + 'đ'}}</td>
                        </tr>
                    </tbody>
                </table>
            </template>
            <div class="text-center" v-else>
                Đang tải...
            </div>
        </div>
    </div>
</template>

<script type="text/javascript">    
    export default {
        computed: {
            items: function() {
                return this.$store.state.items;
            }
        }
    }
</script>

<style scoped>	

</style>
