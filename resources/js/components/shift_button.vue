<template>
    <li>
        <button class="btn" :class="shiftButtonClass" @click="flipShiftActive()">
            {{ buttonMessage }}
        </button>
    </li>
</template>

<script>
    export default {
        data: function() {
            return {
                buttonMessage: "",
                shiftButtonClass: null
            }
        },
        methods: {
            setButtonStatus: function(shift_active) {
                if (shift_active) {
                    this.shiftButtonClass = "btn-danger";
                    this.buttonMessage = "Kết thúc ca";
                } else {
                    this.shiftButtonClass = "btn-success";
                    this.buttonMessage = "Bắt đầu ca";
                }
            },
            flipShiftActive: function() {
                let message = null;

                /////////////////////////////////////////
                // SweetAlert
                if (this.$store.state.user.shift_active) {
                    message = "Đang kết thúc ca...";

                } else {
                    message = "Đang bắt đầu ca...";
                }

                const toast = this.$swal.mixin({
                    toast: true,
                    position: 'top',
                    showConfirmButton: false
                });

                toast({
                    title: message,
                    onBeforeOpen: () => {
                        this.$swal.showLoading();
                    },
                });
                /////////////////////////////////////////

                return this.$http.put('api/users/' + this.$store.state.user.id + "/toggleShift", null)
                    .then((response) => {
                        let responseUser = Object.assign({}, response.data.data);
                        this.$store.commit("setUser", responseUser);
                        this.setButtonStatus(this.$store.state.user.shift_active);
                        this.$root.notifyCounter();

                        if (this.$store.state.user.shift_active) {
                            message = "Ca đã bắt đầu";
                            this.$root.loadActiveData();
                        } else {
                            message = "Ca đã kết thúc";
                            this.$root.clearUserData(false);
                        }

                        /////////////////////////////////////////
                        // SweetAlert
                        const toast = this.$swal.mixin({
                            toast: true,
                            position: 'top',
                            showConfirmButton: false,
                            timer: 3000
                        });
                        toast({
                            title: message,
                            type: 'info'
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
            },
        },
        mounted() {
            this.setButtonStatus(this.$store.state.user.shift_active);
        }
    }
</script>

<style scoped>
    button {
        margin-right: 15px;
        margin-top: 8px;
    }
</style>
