<template>
    <div class="box box-info" v-if="this.$store.state.user.shift_active === 0">
        <div class="box-header with-border">
            <div class="text-center">
                Bạn không trong ca làm
            </div>
        </div>
    </div>
    <div v-else>
        <button class="btn btn-lg btn-block btn-info" @click="newMeal">+ Tạo bữa ăn mới</button>
        <br/>
        <prepared-orders :preparedOrders="preparedOrders" @deliver-click="deliverOrder" ref="preparedOrdersRef"></prepared-orders>

        <template v-for="meal in newWaiterMeals">
            <waiter-new-meal :meal="meal" @created-meal="createdMeal(meal, $event)" :key="meal.id"></waiter-new-meal>
        </template>

        <template v-for="(meal, index) in waiterMeals">
            <meal-box :meal="meal" :mealIndex="index" @terminate-meal="terminateMeal(meal)" :key="meal.id"></meal-box>
        </template>
    </div>
</template>


<script type="text/javascript">
    // Component code
    import PreparedOrders from './waiter/preparedOrders.vue';
    import MealBox from './waiter/mealBox.vue';
    import NewMeal from './waiter/waiterNewMeal.vue';

    export default {
        computed: {
            waiterMeals: function() {
                return this.$store.state.waiter.meals;
            },
            newWaiterMeals: function() {
                return this.$store.state.waiter.newMeals;
            },
            preparedOrders: function() {
                return this.$store.state.waiter.preparedOrders;
            }
        },
        methods: {
            terminateMeal: function (meal) {
                this.$http.post('api/meals/' + meal.id + '/terminate')
                    .then(response => {
                        if (response.status === 201) {
                            this.$store.commit('removeWaiterMeal', meal);
                            this.$socket.emit('propagatePendingInvoice', response.data.data);

                            this.$swal({
                                type: 'success',
                                title: 'Kết thúc',
                                text: 'Bữa ăn đã kết thúc'
                            });
                        }
                    }).catch(() => {
                        this.$swal({
                            type: 'error',
                                title: 'Ồ',
                                text: 'Có gì đó sai sai...',
                        });
                    });
            },
            deliverOrder: function (order, index) {
                this.$http.post('api/orders/' + order.id + '/deliver')
                    .then(response => {
                        if (response.status === 200) {
                            order.state = "delivered";
                            this.$store.commit('removeWaiterPreparedOrders', index);
                            this.$socket.emit('propagateWaiterDeliveries');
                            this.$store.commit('updateWaiterOrder', order);

                            this.$swal({
                                type: 'success',
                                title: 'Đã giao',
                                text: 'Món ăn này đã được giao cho khách',
                            });
                        } else {
                            this.$swal({
                                type: 'error',
                                title: 'Ồ',
                                text: 'Có gì đó sai sai...',
                            });
                        }
                    });
            },
            newMeal: function() {
                this.$swal({
                    title: 'Table number?',
                    input: 'number',
                    showCancelButton: true,
                    confirmButtonText: 'Submit',
                    showLoaderOnConfirm: true,
                    allowOutsideClick: false,
                    preConfirm: (tableNumber) => {
                        return this.$http.get(`/api/tables/${tableNumber}/check`)
                            .then(response => {
                                console.log(response);
                                if (response.data.data) {
                                    if (response.data.data === 'taken') {
                                        return {'status': 'taken'};

                                    } else {
                                        this.$store.commit('addNewWaiterMeal', response.data.data);
                                        return {'status': 'success',
                                                'data': response.data.data};
                                    }
                                }
                            })
                            .catch(payload => {
                                return {'status': 'fail', 'code': payload.response.status};
                            });
                    }
                }).then((result) => {
                    if (result.value.status === 'taken') {
                        /////////////////////////////////////////
                        // SweetAlert
                        this.$swal({
                            type: 'error',
                            title: 'Bàn này đã được đặt',
                            text: "Một bữa ăn đang diễn ra trên bàn này"
                        });
                        /////////////////////////////////////////

                    } else if (result.value.status === 'success') {
                        /////////////////////////////////////////
                        // SweetAlert
                        const toast = this.$swal.mixin({
                            toast: true,
                            position: 'top',
                            showConfirmButton: false,
                            timer: 3000
                        });
                        toast({
                            title: `Một bữa ăn đã được tạo tại bàn ${result.value.data.table_number_id}`,
                            type: 'success'
                        });
                        /////////////////////////////////////////

                    } else {
                        if (result.value.code === 404) {
                            /////////////////////////////////////////
                            // SweetAlert
                            this.$swal({
                                type: 'error',
                                title: 'Bàn này không tồn tại',
                                text: "Số của bàn bạn chọn không tồn tại"
                            });
                            /////////////////////////////////////////

                        } else {
                            /////////////////////////////////////////
                            // SweetAlert
                            this.$swal({
                                type: 'error',
                                title: 'Ồ',
                                text: "Có gì đó sai sai..."
                            });
                            /////////////////////////////////////////
                        }
                    }
                });
            },
            createdMeal: function (meal, orders) {
                this.$store.commit('removeNewWaiterMeal', meal);
                meal.orders = orders;
                this.$store.commit('addWaiterMeal', meal);
            }
        },
        components: {
            'prepared-orders': PreparedOrders,
            'meal-box': MealBox,
            'waiter-new-meal': NewMeal
        },
    }
</script>

<style scoped>

</style>
