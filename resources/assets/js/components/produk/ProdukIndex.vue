<template>
<div class="container">
    <div class="row">
        <div class="col-md-8 col-md-offset-2">
            <ul class="breadcrumb">
              <li><router-link :to="{name: 'IndexDashboard'}">Home</router-link></li>
              <li class="active">Kelola Produk</li>
            </ul>
            <div class="panel panel-default">
                <div class="panel-heading">Kelola Produk</div>

                <div class="panel-body">

                    <router-link 
                      :to="{name: 'CreateProduk'}" 
                      class="btn btn-md btn-primary"> 
                        Tambah Produk
                    </router-link>
                    <br/>
                    <div>
                    <br/>
                    <input type="text" placeholder="Pencarian ..."  v-model="pencarian" class="form-control" />
                    </div>
                    <br/>
                    <div class="table-responsive">
                    
                    <table class="table table-bordered" >
                    <thead>
                        <th>Kode</th>
                        <th>Nama</th>
                        <th>Tipe</th>
                        <th>Harga Beli</th>
                        <th>Harga Jual 1</th>
                        <th>Harga Jual 2</th>
                        <th>Harga Jual 3</th>
                        <th>Aksi</th>
                    </thead>
                    <tbody v-if="produks.length">
                      <tr v-for="produk,index in produks">
                        <td>{{ produk.kode}}</td>
                        <td>{{ produk.nama}}</td>
                        <td>{{ produk.tipe_produk}}</td>
                        <td>{{ produk.harga_beli}}</td>
                        <td>{{ produk.harga_jual_1}}</td>
                        <td>{{ produk.harga_jual_2}}</td>
                        <td>{{ produk.harga_jual_3}}</td>
                        <td>
                        <router-link 
                          :to="{name:'EditProduk' ,params:{id: produk.id}}" 
                          class="btn btn-xs btn-default"
                       >
                          Edit
                       </router-link>
                        <button 
                          class="btn btn-xs btn-danger" 
                          v-on:click="konfirmasiHapus(produk.id,index,produk.nama)"
                        >
                          Hapus
                        </button>
                        </td>
                      </tr>
                    </tbody>
                    </table>
                    <vue-pagination
                      :data="produksData"
                      v-on:pagination-change-page="getResults"
                      :limit="4"
                    >
                    </vue-pagination>
                    <vue-simple-spinner v-if="loading" message="Loading..."></vue-simple-spinner>
                   </div>
                </div>
            </div>
        </div>
    </div>
</div>
</template>


<script>
  export default {
    data: function() {
      return {
        produks: [],
        produksData: {},
        url: window.location.origin + (window.location.pathname).replace("home","produk"),
        pencarian: '',
        loading: true
      }
    },
    mounted()  {
     var app = this;
    app.getResults();
    },
    watch: {
       pencarian: function(newSearch){
         this.getHasilPencarian();
       }  
    },
    methods: {
      getResults(page){
        var app = this;
        if(typeof page == 'undefined'){
          page = 1;
        }
        axios.get(app.url + '/view?page=' + page)
        .then(function(resp){
          app.produks = resp.data.data;
          app.produksData = resp.data;
          app.loading = false;
        })
        .catch(function(resp){
          console.log(resp);
          app.loading = false;
         
        })
      },
      getHasilPencarian(page){
          
        var app = this;
        if(typeof page == 'undefined'){
          page = 1;
        }
        axios.get(app.url + '/search?q='+app.pencarian+'&page=' + page)
        .then(function(resp){
          app.produks = resp.data.data;
          app.produksData = resp.data;
          app.loading = false;
        })
        .catch(function(resp){
          console.log(resp);
          app.loading = false;
         
        })
      },
      deleteEntry(id,index,namaProduk){
          axios.delete(this.url + '/' + id)
          .then((resp) => {
            this.getResults();
            this.alert("Berhasil Menghapus","Berhasil Menghapus Produk " + namaProduk);
          })
          .catch((resp) =>{
            alert("Something Goes Wrong")
            console.log(resp);
          })
      },
      prosesKonfirmasiAdmin(id,index,namaProduk){
          
          axios.get(this.url + '/' + id + '/konfirmasi')
          .then((resp) => {
            this.getResults();
            this.alert("Berhasil Mengkonfirmasi","Berhasil Mengkonfirmasi Produk " + namaProduk);
          })
          .catch((resp) =>{
            alert("Something Goes Wrong")
            console.log(resp);
          })
      },
      konfirmasiHapus(id,index,namaProduk){
      
        this.$swal({
          title: "Yakin Ingin Menghapus Produk " + namaProduk + "?",
          text: "Data yang di hapus tidak akan bisa di kembalikan lagi",
          icon: "warning",
          buttons: true,
          dangerMode: true,
        })
        .then((willDelete) => {
          if (willDelete) {
            this.deleteEntry(id,index,namaProduk);
          }
        })  
      },
      alert(title,pesan){
        this.$swal({
          title: title,
          text: pesan,
          icon: "success"
        });
      }
    }
  }

</script>

