<template>
<div class="container">

     <div class="row">
       
            <div class="col col-md-6">
                <h4>Simple upload</h4>
                <img :src="image" class="img-responsive"><br/>
                <input type="file" v-on:change="onFileChange" class="form-control">
                <button class="btn btn-success btn-block" @click="upload">Upload</button>
            </div>
            <div class="col col-md-6">
                  <h4>Multiple upload</h4>
                 
                 <input type="file" class="iup" ref="imgs" v-on:change="onMultiFileChange" name="" multiple>
                
                 <div class="listImg">
                      <div class="img_item" v-for="(img,i) in files" :key="i">
                           <a class="rm_item" @click="removeFile(i)">x</a>
                          <img :src="img" alt=""  @mouseover="onOverImage(img)">
                         
                      </div>
                      <button @click="addFiles" class="btn_add_img">+</button>
                </div>
            </div>
           
    </div>
    <div class="row">
        <div class="col col-md-6">
               
        </div>
        <div class="col col-md-6">
                <img :src="selectedImg" alt="" width="300" height="300">
        </div>
    </div>
</div>
   
</template>
<script>
    export default{
        data(){
            return {
                image: '',
                files: [],
                selectedImg: ''
            }
        },
        methods: {
          
            onFileChange(e) {
                let files = e.target.files || e.dataTransfer.files;
                if (!files.length)
                    return;
                this.createImage(files[0]);
            },
            createImage(file) {
                let reader = new FileReader();
                let vm = this;
                reader.onload = (e) => {
                    vm.image = e.target.result;
                };
                reader.readAsDataURL(file);
            },
            upload(){
                axios.post('/api/upload',{image: this.image}).then(response => {

                });
            },

            /* multiple files */
           onMultiFileChange(){

               let uploadedFiles = this.$refs.imgs.files;
                for( var i = 0; i < uploadedFiles.length; i++ ){
                    this.createMultiImage(uploadedFiles[i]);
                }
                console.log(this.files)
           },
           createMultiImage(file) {
                let reader = new FileReader();
                let vm = this;
                reader.onload = (e) => {
                    vm.files.push(e.target.result);
                };
                reader.readAsDataURL(file);
            },

            addFiles(){
             this.$refs.imgs.click();
            },
            removeFile( key ){
             this.files.splice( key, 1 );
            },
            async onOverImage(e){
                this.selectedImg = await e
            },
            uploadMultiple(){
                let formData = new FormData();
                for( var i = 0; i < this.files.length; i++ ){
                  let file = this.files[i];
                  formData.append('files[' + i + ']', file);
                }
                axios.post( '/multiple-files', formData, {
                    headers: {
                        'Content-Type': 'multipart/form-data'
                    }
                }).then(function(){
                console.log('SUCCESS!!');
                })
                .catch(function(){
                console.log('FAILURE!!');
                });
            },
            
        }
    }
</script>


<style scoped>
.listImg{
    display: flex;
    flex-wrap: wrap
}
.img_item, .btn_add_img{
    width: 64px;
    height:64px;
    margin: 5px;
transition: all 0.3s
}
.img_item{
  position: relative;
  border: 1px solid #ccc;
}
.img_item img{
    width: 100%;
    height: 100%;
}
.img_item:hover{
     border: 2px solid rebeccapurple;
}
.img_item .rm_item{
    display: none;
    position: absolute;
    padding: 0px 5px;
    background: rebeccapurple;
    color: #fff;
    top: 0;
    right: 0;
    cursor: pointer;
}
.btn_add_img{
    border: 1px dashed rebeccapurple;
    background: transparent;
    color: rebeccapurple;
    outline: none;
}
.btn_add_img:active{
    border-color: red
}
.iup{
    display: none
}
body, html{
    background: #eee;
}
   .row{
       background: #eee;
       width: 100%;
       display: flex;
   }
   .row .col{
       background: #fff;
       padding: 10px;
   }
  
   .row .col-md-12{
      width: 100%;
      margin:  5px 0;
   }
    .row .col-md-6{
       width: calc(50% - 10px);
       margin: 5px;
   }
   .row .col-md-3{
       width: calc(25% - 10px);
       margin: 5px;
   }

</style>

