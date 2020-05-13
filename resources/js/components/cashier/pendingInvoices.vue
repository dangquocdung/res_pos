<template>
    <div>
        <div class="box" :class="{'box-colapsed': !showPending}">
            <div class="box-header with-border">
                <h3 class="box-title">Hóa đơn đang chờ xử lý</h3>

                <div class="box-tools pull-right">
                    <button type="button" class="btn btn-box-tool" data-widget="collapse" data-toggle="tooltip"
                            title="Collapse" v-on:click.prevent="togglePendingBox()">
                        <i class="fa fa-minus"></i></button>
                </div>
            </div>
            <div class="box-body table-responsive">
                <table class="table table-striped">
                    <thead>
                    <tr>
                        <th>Bàn</th>
                        <th>Người phục vụ</th>
                        <th>Tổng cộng</th>
                        <th>Thao tác</th>
                    </tr>
                    </thead>
                    <tbody>
                    <template v-for="(invoice, index) in pendingInvoices">
                        <tr class="table-warning">
                            <td>{{ invoice.table }}</td>
                            <td>{{ invoice.waiter }}</td>
                            <td>{{ invoice.total_price }}</td>
                            <td>
                                <a class="btn btn-sm btn-success" v-on:click.prevent="markPaid(invoice, index)">Đã trả tiền</a>
                            </td>
                        </tr>
                    </template>
                    </tbody>
                </table>
            </div>
        </div>

        <div id="markAsPaid" class="modal fade" role="dialog">
            <div class="modal-dialog" v-if="activeInvoice">
                <div class="modal-content">
                    <div class="modal-header">
                        <button type="button" class="close" data-dismiss="modal">&times;</button>
                        <h4 class="modal-title">Hoá đơn {{ activeInvoice.id }} {{activeInvoice.total_price}}đ - Bàn {{activeInvoice.table}} - {{activeInvoice.date}}</h4>
                    </div>
                    <div class="modal-body">
                        <form class="form-horizontal" data-bitwarden-watching="1">
                            <div class="form-group">
                                <label for="markPaidName" class="col-sm-2 control-label">Tên khách hàng</label>

                                <div class="col-sm-10">
                                    <input type="text" class="form-control" id="markPaidName">
                                </div>
                            </div>
                            <div class="form-group">
                                <label for="markPaidNIF" class="col-sm-2 control-label">Mã số thuế</label>

                                <div class="col-sm-10">
                                    <input type="number" class="form-control" id="markPaidNIF">
                                </div>
                            </div>
                        </form>
                    </div>
                    <div class="modal-footer">
                        <button type="button" class="btn btn-default" data-dismiss="modal">Huỷ</button>
                        <button type="button" class="btn btn-success" v-on:click.prevent="saveInvoice()">Lưu</button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script type="text/javascript">
    // Component code

    module.exports = {
        props: ['pendingInvoices'],
        data: function(){
            return {
                showPending: true,
                activeInvoice: null,
                activeIndex: null
            }
        },
        methods: {
            saveInvoice: function () {
                let name = $('#markPaidName').val()
                let nif = $('#markPaidNIF').val()
                if(name == ''){
                    return this.$swal({
                        type: 'error',
                        title: 'Oops...',
                        text: 'Name is required!',
                    });
                }
                if(nif == ''){
                    return this.$swal({
                        type: 'error',
                        title: 'Oops...',
                        text: 'NIF is required!',
                    });
                }
                this.$emit('mark-paid', this.activeInvoice, this.activeIndex, name, nif);
            },
            markPaid: function (invoice, index) {
                this.activeInvoice = invoice;
                this.activeIndex = index;

                $('#markAsPaid').modal('show');
            },
            togglePendingBox: function () {
                if(this.showPending){
                    this.showPending = false;
                } else {
                    this.showPending = true;
                }
            },
        },
    }
</script>

<style scoped>

</style>
