<template>
    <div class="box" :class="{'box-colapsed': !showPrepared}">
        <div class="box-header with-border">
            <h3 class="box-title">Món đã sẵn sàng</h3>

            <div class="box-tools pull-right">
                <button type="button" class="btn btn-box-tool" data-widget="collapse" data-toggle="tooltip"
                        title="Collapse" v-on:click.prevent="togglePreparedBox()">
                    <i class="fa fa-minus"></i></button>
            </div>
        </div>
        <div class="box-body table-responsive">
            <table class="table table-striped">
                <thead>
                <tr>
                    <th>Món</th>
                    <th>Đầu bếp</th>
                    <th>Thao tác</th>
                </tr>
                </thead>
                <tbody>
                <template v-for="(order, index) in preparedOrders">
                    <tr class="table-warning">
                        <td>{{ order.item.name }}</td>
                        <td>{{ order.responsible_cook.name }}</td>
                        <td>
                            <a class="btn btn-sm btn-warning" v-on:click.prevent="deliverOrder(order, index)">Giao cho khách</a>
                        </td>
                    </tr>
                </template>
                </tbody>
            </table>
        </div>
    </div>
</template>

<script type="text/javascript">
    // Component code

    module.exports = {
        props: ['preparedOrders'],
        data: function(){
            return {
                showPrepared: true
            }
        },
        methods: {
            deliverOrder: function (order, index) {
                this.$emit('deliver-click', order, index);
            },
            togglePreparedBox: function () {
                if(this.showPrepared){
                    this.showPrepared = false;
                } else {
                    this.showPrepared = true;
                }
            },
        },
    }
</script>

<style scoped>

</style>
