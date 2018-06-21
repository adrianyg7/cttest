<template>
    <div>
        <h1>Products</h1>

        <form @submit.prevent="save" action="/products" method="POST">
            <div class="form-group">
                <label for="name">Product name</label>
                <input type="text" class="form-control" id="name" v-model="name" required />
            </div>
            <div class="form-group">
                <label for="quantity">Quantity</label>
                <input type="number" class="form-control" id="quantity" v-model="quantity" required />
            </div>
            <div class="form-group">
                <label for="price">Price per item</label>
                <input type="number" class="form-control" id="price" v-model="price" required />
            </div>
            <div class="form-group">
                <button type="submit" class="btn btn-success">{{ editing ? 'Update' : 'Submit' }}</button>
                <button @click="cancel" type="button" class="btn btn-default">Cancel</button>
            </div>
        </form>

        <div class="table-responsive">
            <table class="table table-bordered">
                <thead>
                    <tr>
                        <th>Product name</th>
                        <th>Quantity in stock</th>
                        <th>Price per item</th>
                        <th>Datetime submitted</th>
                        <th>Total value number</th>
                        <th>Actions</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="(product, index) in products">
                        <th>{{ product.name }}</th>
                        <th>{{ product.quantity }}</th>
                        <th>{{ product.price }}</th>
                        <th>{{ product.date }}</th>
                        <th>{{ product.quantity * product.price }}</th>
                        <th>
                            <button @click="edit(product, index)" class="btn btn-xs">Edit</button>
                        </th>
                    </tr>
                </tbody>
                <tfoot>
                    <tr>
                        <th colspan="4">Sum Total Value numbers</th>
                        <th>{{ totalSum }}</th>
                        <th>&nbsp;</th>
                    </tr>
                </tfoot>
            </table>
        </div>
    </div>
</template>

<script>
    const defaultProduct = {
        name: '',
        quantity: 1,
        price: 0,
    };

    export default {
        data() {
            return {
                name: defaultProduct.name,
                quantity: defaultProduct.quantity,
                price: defaultProduct.price,
                products: [],
                editing: false,
                index: null
            };
        },

        computed: {
            totalSum() {
                return this.products.reduce((acc, product) => {
                    return acc + product.quantity * product.price;
                }, 0);
            }
        },

        mounted() {
            axios.get('products')
                .then(response => {
                    this.products = response.data;
                });
        },

        methods: {
            save() {
                if (this.editing) {
                    this.update();
                } else {
                    this.store();
                }
            },

            store() {
                this.products.push({
                    name: this.name,
                    quantity: this.quantity,
                    price: this.price,
                    date: moment().format('YYYY-MM-DD h:mm:ss'),
                });

                axios.post('products', { products: this.products })
                    .then(response => {
                        this.clear();
                    });
            },

            update() {
                let product = this.products[this.index];
                product.name = this.name;
                product.quantity = this.quantity;
                product.price = this.price;

                axios.post('products', { products: this.products })
                    .then(response => {
                        this.clear();
                    });
            },

            cancel() {
                this.editing = false;
                this.clear();
            },

            edit(product, index) {
                this.editing = true;
                this.index = index;
                this.name = product.name;
                this.quantity = product.quantity;
                this.price = product.price;
            },

            clear() {
                this.index = null;
                this.name = defaultProduct.name;
                this.quantity = defaultProduct.quantity;
                this.price = defaultProduct.price;
            }
        }
    }
</script>
