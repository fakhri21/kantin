<template>
    <div>
        <!-- Content Header (Page header) -->
        <div class="content-header">
            <div class="container-fluid">
                <div class="row mb-2">
                    <div class="col-sm-6">
                        <h1 class="m-0">Pilihan Menu Produk</h1>
                    </div><!-- /.col -->
                    <div class="col-sm-6">
                    </div><!-- /.col -->
                </div><!-- /.row -->
            </div><!-- /.container-fluid -->
        </div>
        <!-- /.content-header -->

        <!-- Main content -->
        <div class="content">
            <div class="container-fluid">
                <b-row class="mb-3">
                    <b-col cols=4>
                        <b-button v-b-modal.modal-cart><i class="fa fa-cart-arrow-down" aria-hidden="true"></i> Cart</b-button>
                    </b-col>
                </b-row>
                <b-row>
                    <b-col cols=12 v-for="item in items_produk" :key="item.uniqid" class="card card-solid">
                        <div>
                        <div class="card-body">
                            <div class="row">
                                <div class="col-12 col-sm-6">
                                    <b-row class="mb-3">
                                        <b-col>
                                            <h3>{{item.nama_produk}}</h3>
                                            <h4>{{item.harga|currency}}</h4>
                                        </b-col>
                                    </b-row>
                                </div>
                            </div>
                            <div class="row">
                                <div class="col-6">
                                       <div v-if="item.status==1" class="btn btn-primary btn-flat" @click="addToCart(item);calcSum()">
                                            <i class="fas fa-cart-plus"></i>
                                            Add to Cart
                                        </div>
                                        <div v-else>
                                            <b-badge pill variant="secondary">Habis</b-badge>  
                                        </div>
                                </div>
                            </div>
                        </div>

                    </div>
                    

                    </b-col>
                </b-row>
                <b-row>
              
                </b-row>
                
  <b-modal @ok="checkout()" id="modal-cart" title="Cart">
      <b-row>
          <b-col>
            <b-table ref="table" stacked striped hover :fields="fields"  :items="items_cart">
                <template #cell(qty)="row">
                    {{row.item.qty}}
                    <b-button @click="addqty(row.index)"><i class="fa fa-plus" aria-hidden="true"></i></b-button>
                    <b-button @click="reduceqty(row.index)"><i class="fa fa-minus" aria-hidden="true"></i></b-button>
                </template>
                <template #cell(keterangan)="row">
                      <textarea v-model="items_cart[row.index].keterangan" class="form-control" name="" id="" rows="3"></textarea>
                </template>
                
            </b-table>
          </b-col>
      </b-row>
      <b-row>
          <b-col cols=12>
              Total Keseluruhan : {{grand_total |currency}}
          </b-col>
          <b-col cols=12>
              No Kamar  <b-form-input v-model="no_kamar"
                            type="text"
                            placeholder="Masukkan nomor kamar"
              ></b-form-input>

          </b-col>
      </b-row>


  </b-modal>



                </div>
                <!-- /.row -->
            </div><!-- /.container-fluid -->
        </div>
        <!-- /.content -->
</template>


<script>
    const formatTime = second => new Date(second * 1000).toISOString().substr(11, 8);

    module.exports = {
        computed: {

        },
        data() {
            return {
                grand_total: 0,
                no_kamar:'',
                items_cart: [],
                fields: [{
                        key: 'nama_produk',
                        label: 'Nama Produk'
                    },
                    {
                        key: 'harga',
                        label: 'Harga'
                    },
                    {
                        key: 'qty',
                        label: 'Qty'
                    },
                    {
                        key: 'keterangan',
                        label: 'Keterangan'
                    },
                    
                    {
                        key: 'total',
                        label: 'Total'
                    }
                ],
                items_produk: [{
                        uniqid: 111,
                        nama_produk: "Nasi Goreng"
                    },
                    {
                        uniqid: 112,
                        nama_produk: "Ayam goreng"
                    },
                ],

            }
        },

        methods: {
            calcSum() {

                let total = 0;
                this.items_cart.forEach((item, i) => {
                    item.total = item.harga * item.qty;
                    total += item.harga * item.qty;

                });
                this.grand_total = total;
            },

            showAll() {
                var start_index = this.indexAwal

                this.loading = true
                var _this = this

                axios.get(base_url + "M_produk").then(function (response) {

                        // _this.rows = response.data.recordsTotal
                        _this.items_produk = response.data.data

                        console.log(response.data.data)
                    })
                    .catch((error) => {
                        Swal.fire({
                            icon: 'error',
                            title: 'Maaf Terjadi Kesalahan',
                            text: 'Silahkan coba kembali'
                        })
                    })
                    .finally(() => {
                        this.loading = false
                    });
            },
            addToCart(product) {
                const locationInCart = this.items_cart.findIndex(p => {
                    return p.uniqid === product.uniqid
                })
                if (locationInCart === -1) {
                    product.qty = 1
                    this.items_cart.push(product)
                } else {
                    this.items_cart[locationInCart].qty++
                }
                Swal.mixin({
                    toast: true,
                    icon: 'success',
                    position: 'top-end',
                    title: 'Berhasil Menambahkan',
                    showConfirmButton: false,
                    timer: 3000,
                }).fire()
            },
            addqty(id) {
                var qty=this.items_cart[id].qty+1
                    this.$set(this.items_cart[id], 'qty', qty)
                    this.$refs.table.refresh();
                    console.log(this.items_cart)
                },
            reduceqty(id) {
                if (this.items_cart[id].qty <= 1) {
                    this.items_cart.splice(id, 1)
                } else {
                    this.items_cart[id].qty--
                }
                this.$refs.table.refresh();
            },
            removeFromCart(id) {
                this.cart.splice(id, 1)
            },
   

            checkout() {
                Swal.fire({
                    title: 'Apakah pesanan anda sudah benar?',
                    text: "Barang yang sudah dibeli tidak dapat dikembalikan",
                    showCancelButton: true,
                    confirmButtonColor: '#3085d6',
                    cancelButtonColor: '#d33',
                    confirmButtonText: 'Pesan'
                }).then((result) => {
                    if (result.isConfirmed) {
                        this.data_checkout = {
                            id_user: localStorage.getItem('id_user'),
                            total: this.grand_total,
                            no_kamar:this.no_kamar,
                            items: JSON.stringify(this.items_cart)

                        }
                        axios.post(base_url + "/Pesanan", Qs.stringify(this.data_checkout))
                            .then(function (response) {
                                Swal.fire('Pemesanan Berhasil, silahkan menuju ke kasir', '', 'success')
                                v.nama_user = localStorage.getItem('nama_user')
                                v.$router.push('/')
                            })
                            .catch((err) => {
                                console.log(err)
                                Swal.fire({
                                    icon: 'error',
                                    title: 'Maaf Terjadi Kesalahan',
                                    html: err.response.data.status
                                })
                            })
                            .finally(() => {
                                this.loading = false
                            });
                    }
                })


            }
        },

        mounted() {
            this.showAll()

        },


    }
</script>

<style>
    .invisible {
        visibility: hidden;
    }
</style>