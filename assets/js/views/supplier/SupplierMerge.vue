<template>
    <modal
        id="supplierMergeModal"
        classes="modal-info"
        :confirm-action="mergeSuppliers"
        :confirm-enabled="isTargetSet()"
    >
        <template slot="header">
            Merge Suppliers
        </template>

        <optionliststatic
            v-model="targetSupplier"
            label="Merge suppliers in to"
            display-property="title"
            :display-text-fn="item => item.id + ': ' + item.title"
            property="id"
            :preloaded-options="selectedSuppliers"
            empty-string="-- Select a Destination Supplier --"
            :chosen="false"
        />

        <template v-if="isTargetSet()">
            <div class="form-group">
                <div class="checkbox">
                    <label for="addresses">
                        <input
                            id="addresses"
                            v-model="mergeContext"
                            type="checkbox"
                            value="addresses"
                        >
                        Merge Addresses
                    </label>
                </div>
                <div class="checkbox">
                    <label for="contacts">
                        <input
                            id="contacts"
                            v-model="mergeContext"
                            type="checkbox"
                            value="contacts"
                        >
                        Merge Contacts
                    </label>
                </div>
                <div class="checkbox">
                    <label for="orders">
                        <input
                            id="orders"
                            v-model="mergeContext"
                            type="checkbox"
                            value="orders"
                        >
                        Merge Supply Orders
                    </label>
                </div>
                <div class="checkbox">
                    <label for="orders">
                        <input
                            id="deactivate"
                            v-model="mergeContext"
                            type="checkbox"
                            value="deactivate"
                        >
                        Deactivate the merged {{ selectedSupplierList.length }} supplier(s)
                    </label>
                </div>
            </div>
            Merge the following suppliers in to <strong>{{ targetSupplier.id }} - {{ targetSupplierTitle }}</strong>
            <ul>
                <li
                    v-for="supplier in selectedSupplierList"
                    :key="supplier.id"
                    v-text="supplier"
                />
            </ul>
        </template>

        <template slot="confirmButton">
            Merge
        </template>
    </modal>
</template>


<script>
    import Modal from '../../components/Modal.vue';
    import OptionListStatic from '../../components/OptionListStatic.vue';
    export default {
        components: {
            'modal' : Modal,
            'optionliststatic' : OptionListStatic
        },
        name: 'SupplierMerge',
        props: {
            selectedSupplierIds: { type: Array, required: true }
        },
        data() {
            return {
                targetSupplier: {},
                mergeContext: ['orders', 'deactivate'],
            };
        },
        computed: {
            selectedSuppliers: function () {
                let me = this;
                return this.selectedSupplierIds.map(supplierId => me.$store.getters.getSupplierById(supplierId));
            },
            targetSupplierTitle: function () {
                if (this.isTargetSet()) {
                    let target = this.$store.getters.getSupplierById(this.targetSupplier.id);
                    return target.title;
                }

                return '';
            },
            selectedSupplierList: function () {
                if (!this.isTargetSet()) return [];
                let me = this;
                let suppliers = this.selectedSupplierIds.map(supplierId => me.$store.getters.getSupplierById(supplierId));
                suppliers = suppliers.filter(supplier => supplier.id !== me.targetSupplier.id);
                return suppliers.map(supplier => supplier.id + ": " + supplier.title)
            },
        },
        created() {
            console.log('Component mounted.')
        },
        mounted() {
            this.$store.dispatch('loadSuppliers');
        },
        methods: {
            mergeSuppliers: function() {
                let me = this;
                axios
                    .post('/api/suppliers/merge', {
                            targetSupplier: this.targetSupplier.id,
                            sourceSuppliers: this.getSourcesSupplierIds(),
                            context: this.mergeContext,
                    })
                    .then(response => me.$parent.refreshTable())
                    .catch(function (error) {
                        console.log(error);
                    });
                console.log('send the merge');
            },
            fetchTargetSupplier: function() {
                if (this.targetSupplier.hasOwnProperty('id')) {
                    return this.$store.getters.getSupplierById(this.targetSupplier.id);
                }
            },
            isTargetSet: function () {
                return this.targetSupplier.hasOwnProperty('id') && !!this.targetSupplier.id;
            },
            getSourcesSupplierIds: function () {
                if (!this.isTargetSet()) return [];
                let me = this;
                let suppliers = this.selectedSupplierIds.map(supplierId => me.$store.getters.getSupplierById(supplierId));
                suppliers = suppliers.filter(supplier => supplier.id !== me.targetSupplier.id);
                return suppliers.map(supplier => supplier.id);
            },
            reset: function() {
                this.targetSupplier = {};
                this.mergeContext = ['orders', 'deactivate'];
            }
        }
    }
</script>
