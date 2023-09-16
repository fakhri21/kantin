<template>
<div>
<!-- Content Header (Page header) -->
      <div class="content-header">
        <div class="container-fluid">
          <div class="row mb-2">
            <div class="col-sm-6">
              <h1 class="m-0">Detail Pesanan </h1>
            </div><!-- /.col -->
            <div class="col-sm-6">
            </div><!-- /.col -->
          </div><!-- /.row -->
        </div><!-- /.container-fluid -->
      </div>
      <!-- /.content-header -->
  
      <!-- Main content -->
      <div class="content">
        <b-container ref="data_reservasi" id="data_reservasi" class="bv-example-row">
              <b-row class="mb-10">
                <b-col>
                  <h3>{{items_produk[0].no_pesanan}}</h3>
                  <p>NIK {{items_produk[0].no_ktp}}</p>
                  <p>Atas Nama {{items_produk[0].first_name}} {{items_produk[0].last_name}}</p>
                  <p>Email : {{items_produk[0].email}} </p>
                  <p>No. Hp : {{items_produk[0].no_hp}} </p>
                  
                  <h3>No. Kamar : {{items_produk[0].no_kamar}} </h3>
                </b-col>
              </b-row>
              <b-row>
                <b-col>
                    <b-table striped hover
                           :fields="fields"
                           :items="items_produk"
                    >
                  </b-table>
                </b-col>
              </b-row>
              <b-row>
                <b-col>
                  <h3>Total <b-badge pill variant="success">{{items_produk[0].total|currency}}</b-badge></h3>
                  <br>
                </b-col>
              </b-row>                    
            </b-container>
              <b-row>
                <b-col>
                  <b-button v-if="items_produk[0].status==0"  variant="primary" @click="terima_pembayaran()">
                      Terima Pembayaran
                  </b-button>

                  <b-button @click="print()">Print</b-button>
                </b-col>
              </b-row>                    
      </div>
      <!-- /.content -->    
</div>    
</template>


<script>
    const formatTime = second => new Date(second * 1000).toISOString().substr(11, 8);

    module.exports =  {
        
        data() {
            return {
              items_produk: [],
              fields: [{
                  key: 'produk',
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
                  key: 'sub_total',
                  label: 'Sub Total'
                }
              ],
              data_reservasi: []
            }
          },

          methods: {
            
          print(){
            var elem = this.$refs.data_reservasi.innerHTML;
            console.log(elem)
            print_pdf(elem)
          },

            terima_pembayaran() {
              this.loading = true
              axios.post(base_url + "Pesanan/terima_pembayaran/",Qs.stringify({
                    id: this.$route.params.id
                }))
                .then(function (response) {
                  Swal.fire('Pembayaran Berhasil', '', 'success')
                  v.$router.push('/daftar_pesanan')
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
            },

            showAll() {
              var start_index = this.indexAwal

              this.loading = true
              var _this = this

              axios.get(base_url + "Pesanan/detail/", {
                  params: {
                    id: this.$route.params.id
                  }
                }).then(function (response) {

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

          },
          mounted() {
            this.showAll()
          }

        }
</script>

<style>
  .invisible {
    visibility: hidden;
  }
</style>