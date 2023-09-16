<template>
<div>
<!-- Content Header (Page header) -->
      <div class="content-header">
        <div class="container-fluid">
          <div class="row mb-2">
            <div class="col-sm-6">
              <h1 class="m-0">Daftar Menu</h1>
            </div><!-- /.col -->
            <div class="col-sm-6">
            </div><!-- /.col -->
          </div><!-- /.row -->
        </div><!-- /.container-fluid -->
      </div>
      <!-- /.content-header -->
  
      <!-- Main content -->
      <div class="content">
          <b-container class="bv-example-row">
            <b-row>
              <b-col>
                <b-button v-b-modal.modal_tambah>Tambah Produk</b-button>
                   <!-- Modal Component -->
                <b-modal id="modal_tambah" title="Masukkan Produk" @ok="submit_produk">
                  <form @submit.stop.prevent="submit">
                    <b-form-input type="text" placeholder="Nama Produk" v-model="nama_produk"></b-form-input>
                    <b-form-input type="text" placeholder="Harga" v-model="harga"></b-form-input>
                    <b-form-input type="text" placeholder="Jenis" v-model="jenis"></b-form-input>
                  </form>
                </b-modal>
              </b-col>
            </b-row>
              <b-row>
                <b-col>
                    <b-table striped hover
                           :items="produk_items"
                           :fields="fields"
                           stacked
                  >
                    <template #cell(status)="row">
                      <div v-if="row.item.status==0">
                        <b-badge  pill variant="warning">Habis</b-badge>
                        <b-button @click="ubah_status(row.item.uniqid,1)"  variant="warning" href="">
                          Ubah Status
                        </b-button>
                      </div>
                      <div v-else >
                        <b-badge pill variant="success">Tersedia</b-badge>
                        <b-button @click="ubah_status(row.item.uniqid,0)"  variant="primary" href="">
                          Ubah Status
                        </b-button>
                      </div>
                    </template>
                    <template #cell(actions)="row">
                      <b-button size="sm" @click="hapus(row.item.uniqid)">Hapus</b-button>
                    </template>
                  </b-table>
                </b-col>
              </b-row>                  
          </b-container>
      </div>
      <!-- /.content -->    
</div>    
</template>


<script>
    const formatTime = second => new Date(second * 1000).toISOString().substr(11, 8);

    module.exports = {

      data() {
        return {
          produk_items:[],
          fields: [{
              key: 'nama_produk',
              label: 'Nama Produk'
            },
            {
              key: 'harga',
              label: 'Harga'
            },
            {
              key: 'jenis',
              label: 'Jenis'
            },
            {
              key: 'status',
              label: 'Status'
            },
            {
              key: 'actions',
              label: 'Actions'
            }
          ],
          
          nama_produk:'',
          harga:'',
          jenis:'',

        }
      },

      methods: {
      
        showAll() {
          var start_index = this.indexAwal

          this.loading = true
          var _this = this

          axios.get(base_url + "M_produk").then(function (response) {

              // _this.rows = response.data.recordsTotal
              _this.produk_items = response.data.data

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
        
        hapus(id) {
          this.loading = true
          var _this = this

          var data_produk={
            'uniqid':id,
          }

          axios.post(base_url + "M_produk/hapus",Qs.stringify(data_produk)).then(function (response) {
              Swal.fire("Berhasil Menghapus")
              _this.showAll()

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
        
        ubah_status(id,status) {
          this.loading = true
          var _this = this

          var data_produk={
            'uniqid':id,
            'status':status,
          }

          axios.post(base_url + "M_produk/ubah_status",Qs.stringify(data_produk)).then(function (response) {
              Swal.fire("Berhasil Mengubah")
              _this.showAll()

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

        submit_produk() {
          var start_index = this.indexAwal

          this.loading = true
          var _this = this

          var data_produk={
            'nama_produk':_this.nama_produk,
            'harga':_this.harga,
            'jenis':_this.jenis,
          }

          axios.post(base_url + "M_produk",Qs.stringify(data_produk)).then(function (response) {
              Swal.fire("Berhasil Menambah Produk")
              _this.showAll()

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