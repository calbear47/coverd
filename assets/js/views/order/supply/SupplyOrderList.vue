<template>
    <section class="content">
        <router-link
            to="/orders/supply/new"
            class="btn btn-success btn-flat pull-right"
        >
            <i class="fa fa-plus-circle fa-fw" />Create Supply Order
        </router-link>
        <h3 class="box-title">
            Supply Orders List
        </h3>

        <div class="row">
            <div class="col-xs-12">
                <div class="box">
                    <div class="box-header">
                        <!--
                        <div class="box-tools">
                            <div class="input-group input-group-sm" style="width: 150px;">
                                <input type="text" name="table_search" class="form-control pull-right" placeholder="Search">

                                <div class="input-group-btn">
                                    <button type="submit" class="btn btn-default"><i class="fa fa-search"></i></button>
                                </div>
                            </div>
                        </div>
                        -->
                    </div>
                    <!-- /.box-header -->
                    <div class="box-body table-responsive no-padding">
                        <tablepaged
                            :columns="columns"
                            api-url="/api/orders/supply"
                            edit-route="/orders/supply/"
                            :sort-order="[{ field: 'id', direction: 'desc'}]"
                        />
                    </div>
                    <!-- /.box-body -->
                </div>
                <!-- /.box -->
            </div>
        </div>
    </section>
</template>

<script>
    import TablePaged from '../../../components/TablePaged.vue';
    export default {
        components: {
            'tablepaged' : TablePaged
        },
        props:[],
        data() {
            return {
                orders: {},
                columns: [
                    { name: '__checkbox', title: "#" },
                    { name: '__slot:link', title: "Order Id", sortField: 'id' },
                    { name: 'supplier.title', title: "Supplier", sortField: 'supplier.title' },
                    { name: 'warehouse.title', title: "Warehouse", sortField: 'warehouse.title' },
                    { name: 'status', title: "Status", sortField: 'status' },
                    { name: 'createdAt', title: "Created", callback: 'dateTimeFormat', sortField: 'createdAt' },
                    { name: 'updatedAt', title: "Last Updated", callback: 'dateTimeFormat', sortField: 'updatedAt' },
                ]
            };
        },
    }
</script>
