<template>
    <div class="box box-info">
        <div class="box-header with-border">
            <h3 class="box-title">Món đang yêu cầu</h3>
        </div>
        <div class="box-body table-responsive">
            <template v-if="orders">
                <table class="table table-striped">
                    <thead>
                    <tr>
                        <th>Món</th>
                        <th>Tình trạng</th>
                        <th>Thao tác</th>
                    </tr>
                    </thead>
                    <tbody>
                        <tr class="table-warning" v-for="order in orders" :key="order.id">
                            <td>{{ order.item.name }}</td>
                            <td>
                                <span class="label"
                                      :class="order.state === 'confirmed' ? 'label-success' : 'label-warning'">
                                    {{ order.state }}
                                </span>
                            </td>
                            <td>
                                <template v-if="order.state === 'confirmed'">
                                    <button class="btn btn-xs btn-primary"
                                            @click="setState(order, 'on preparation', 'updateOrders')">
                                        Đang chuẩn bị
                                    </button>
                                </template>
                                <button class="btn btn-xs btn-danger"
                                        @click="setState(order, 'prepared', 'deleteOrder')">
                                    Đã sẵn sàng
                                </button>
                            </td>
                        </tr>
                    </tbody>
                </table>
            </template>
            <div class="text-center" v-else-if="this.$store.state.user.shift_active === 0">
                Bạn không trong ca làm
            </div>
            <div class="text-center" v-else>
                Đang tải...
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        computed: {
            orders: function() {
                return this.$store.state.orders;
            }
        },
        methods: {
            setState: function(order, state, commitString) {
                let changedOrder = Object.assign({}, order);
                let formerState = changedOrder.state;
                changedOrder.state = state;

                /////////////////////////////////////////
                // SweetAlert
                const toast = this.$swal.mixin({
                    toast: true,
                    position: 'top',
                    showConfirmButton: false
                });
                toast({
                    title: 'Changing order state...',
                    onBeforeOpen: () => {
                        this.$swal.showLoading();
                    }
                });
                /////////////////////////////////////////

                this.$http.put('/api/orders/' + changedOrder.id, {'state': state})
                    .then((response) => {
                        this.$store.commit(commitString, response.data.data);
                        // io socket communication here
                        if (formerState === 'confirmed') {
                            this.$socket.emit('propagateCookOrderToCooks', response.data.data);
                        }

                        this.$socket.emit('propagateCookOrderToWaiter', response.data.data);

                        /////////////////////////////////////////
                        // SweetAlert
                        const toast = this.$swal.mixin({
                            toast: true,
                            position: 'top',
                            showConfirmButton: false,
                            timer: 3000
                        });
                        toast({
                            title: 'Xong!',
                            type: 'success'
                        });
                        /////////////////////////////////////////
                    })
                    .catch(() => {
                        /////////////////////////////////////////
                        // SweetAlert
                        this.$swal({
                            type: 'error',
                            title: 'Ồ',
                            text: "Có gì đó sai sai..."
                        });
                        /////////////////////////////////////////
                    });
            }
        }
    }
</script>

<style scoped>

</style>
