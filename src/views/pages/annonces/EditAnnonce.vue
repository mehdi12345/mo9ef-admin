<template>
  <Layout>
    <PageHeader  />
    <div class="row">
      <div class="col-xl-12">
        
  <div class="card">
    <div class="card-body">
      <br />
      <br /><br />
            <form class="container"  @submit.prevent="modifier()">
        <div class="row">
          <div class="col-lg-2 col-md-1"></div>
          <div
            class="col-lg-8 col-md-10"
            style="
              border-radius: 20px;
              padding: 20px;
              box-shadow: 1px 1px 1.5px rgb(221, 221, 221),
                0em 0em 1em rgb(221, 221, 221);
            "
          >
            <div class="row">
              <div class="col-lg-6 col-md-12">
                <div class="row">
                  <div class="col-lg-8">
                    <label for=""><h5>{{ $t("category") }}</h5></label>
                  </div>
                </div>
                <div :class="errCatigory ? 'err' : ''">
                  <multiselect
                  v-model="catigore"
                  label="text"
                  :options="Listcatigores"
                  :placeholder="$t('choose_category')"
                  selectLabel=""
                  selectedLabel=""
                  deselectLabel=""
                ></multiselect>
                  
                </div>
              </div>
              <div class="col-lg-6 col-md-12">
                <div class="row">
                  <div class="col-lg-8">
                    <label for=""><h5>{{ $t("ville") }}</h5></label>
                  </div>
                </div>
                <div :class="errVille ? 'err' : ''">
                  <multiselect
                  v-model="city"
                  :options="citys"
                  :placeholder="$t('choose_ville')"
                  selectLabel=""
                  selectedLabel=""
                  deselectLabel=""
                ></multiselect>
                </div>
              </div>
            </div>
  
            <div class="row">
              <!-- *************************************************************************************** -->
  
              <div
                class="col-lg-6 col-md-12"
                v-if="
                  catigore.id == 'CBgR4EvCtyodV2EqLeKg' ||
                  catigore.id == 'a3S74jwragQi3pUMwcte'
                "
              >
                <label for=""
                  ><h5>{{ $t("machine_model") }}</h5></label
                >
                <input
                  required
                  type="text"
                  class="inputText"
                  :placeholder="$t('machine_model')"
                  v-model="modelMachine"
                />
              </div>
              <div
                class="col-lg-6 col-md-12"
                v-if="
                  catigore.id == 'CBgR4EvCtyodV2EqLeKg' ||
                  catigore.id == 'a3S74jwragQi3pUMwcte'
                "
              >
                <label for=""><h5>{{ $t("machine_category") }}</h5></label>
  
                <div :class="errMaciCat ? 'err' : ''">
                  <multiselect
                  v-model="Machine_categorie"
                  :options="ListMachine_categories"
                  :placeholder="$t('machine_category')"
                  label="text"
                  selectLabel=""
                  selectedLabel=""
                  deselectLabel=""
                ></multiselect>
                </div>
              </div>
  
              <!-- *************************************************************************************** -->
              <div
                class="col-lg-6 col-md-12"
                v-if="catigore.id == 'rwAAHojffDZP9U4URtvN'"
              >
                <div class="row">
                  <div class="col">
                    <div class="row">
                      <div class="col">
                        <label for=""><h5>{{ $t("ville_de_depart") }}</h5></label>
                      </div>
                    </div>
                    <div :class="errVilleD ? 'err' : ''">
                      <multiselect
                  v-model="cityD"
                  :options="citys"
                  :placeholder="$t('ville_de_depart')"
                  selectLabel=""
                  selectedLabel=""
                  deselectLabel=""
                ></multiselect>
                    </div>
                  </div>
                  <div class="col">
                    <div class="row">
                      <div class="col">
                        <label for=""><h5>{{ $t("ville_de_arrivee") }}</h5></label>
                      </div>
                    </div>
                    <div :class="errVilleA ? 'err' : ''">
                      <multiselect
                  v-model="cityA"
                  :options="citys"
                  :placeholder="$t('ville_de_arrivee')"
                  selectLabel=""
                  selectedLabel=""
                  deselectLabel=""
                ></multiselect>
                    </div>
                  </div>
                </div>
              </div>
              <!-- *************************************************************************************** -->
  
              <div class="col-lg-6 col-md-12">
                <label for=""
                  ><h5>{{ $t("prix") }}</h5></label
                >
                <input
                  required
                  class="inputText"
                  type="number"
                  v-model="prix"
                  :placeholder="$t('prix')"
                />
              </div>
              <div
                class="col-lg-6 col-md-12"
                v-if="catigore.id == 'rwAAHojffDZP9U4URtvN'"
              >
                <label for=""
                  ><h5>{{ $t("longueur_machine") }}</h5></label
                >
                <input
                  required
                  class="inputText"
                  type="number"
                  v-model="longeur"
                  :placeholder="$t('longueur_machine')"
                />
              </div>
  
              <div
                class="col-lg-6 col-md-12"
                v-if="catigore.id == 'CBgR4EvCtyodV2EqLeKg'"
              >
                <h5>{{ $t("type_of_annonce") }}</h5>
  
                <div :class="errType ? 'err' : ''">
                  <multiselect
                  v-model="type"
                  :options="ListType"
                  :placeholder="$t('type_of_annonce')"
                  selectLabel=""
                  selectedLabel=""
                  deselectLabel=""
                ></multiselect>
                </div>
              </div>
  
              <!-- *************************************************************************************** -->
  
              <!-- *************************************************************************************** -->
  
              <div
                class="col-lg-6 col-md-12"
                v-if="catigore.id != 'a3S74jwragQi3pUMwcte'"
              >
                <label for=""
                  ><h6>{{ $t("weight") }}</h6></label
                >
                <input
                  required
                  class="inputText"
                  type="number"
                  v-model="poids"
                  :placeholder="$t('weight')"
                />
              </div>
  
              <!-- *************************************************************************************** -->
  
              <div class="col-lg-6 col-md-12">
                <label for=""
                  ><h5>{{ $t("name") }}</h5></label
                >
                <input
                  required
                  class="inputText"
                  type="text"
                  :placeholder="$t('name')"
                  v-model="fullname"
                />
              </div>
              <div class="col-lg-6 col-md-12">
                <label for=""
                  ><h5>{{ $t("phone") }}</h5></label
                >
                <input
                  required
                  class="inputText"
                  type="text"
                  v-model="phone"
                  :placeholder="$t('phone')"
                />
              </div>
  
              <div
                class="col-lg-6 col-md-12"
                v-if="catigore.id == 'WHoFjiJf8T126Cy0F99n'"
              >
                <label for=""
                  ><h5>{{ $t("litre") }}</h5></label
                >
                <input
                  required
                  class="inputText"
                  type="number"
                  v-model="litre"
                  :placeholder="$t('litre')"
                />
              </div>
  
              <div class="col-lg-6 col-md-12">
                <label for=""
                  ><h5>{{ $t("title") }}</h5></label
                >
                <input
                  required
                  class="inputText"
                  type="text"
                  v-model="title"
                  :placeholder="$t('title')"
                />
              </div>
              <div class="col-lg-6 col-md-12">
                <label for=""
                  ><h5>{{ $t("description") }}</h5></label
                >
                <textarea
                  required
                  class="inputText"
                  type="text"
                  v-model="descreption"
                  :placeholder="$t('description')"
                />
              </div>
  
              <!-- *************************************************************************************** -->
            </div>
            <br />
            <br />
            <br />
            <center>
              <div style="margin-top: 20px" class="col-lg-6 col-md-12">
                <button
                  class="btn"
                  style="
                    width: 68%;
                    background-color: rgb(255, 174, 0);
                    color: #fff;
                    font-size: 20px;
                  "
                  v-if="!send"
                >
                  {{ $t("edit") }}
                </button>
                <div
                  v-if="send"
                  class="btn"
                  style="
                    width: 68%;
                    background-color: rgb(255, 174, 0);
                    color: #fff;
                    font-size: 20px;
                  "
                >
                  <img
                    src="../../../assets/loading-balls.svg"
                    alt=""
                    width="24"
                    height="24"
                  />
                </div>
              </div>
            </center>
          </div>
        </div>
      </form>
    </div>
    </div>
      </div>
    </div>
  </Layout>
  </template>
  
<style src="vue-multiselect/dist/vue-multiselect.min.css"></style>
  <script>
  
import Layout from "../../layouts/main";
import PageHeader from "@/components/page-header";
  import Listcitys from "../../../assets/ma.json";
  import firebase from "firebase/app";
import Multiselect from "vue-multiselect";
import "firebase/firestore";  
  export default {
    components: {
      Multiselect,
    Layout,
    PageHeader,
    },
    data() {
      return {
        Listcatigores:[],
        ListMachine_categories:[],
        errImages: false,
        errVille: false,
        errCatigory: false,
        errVilleD: false,
        errVilleA: false,
        errType: false,
        errMaciCat: false,
        send: false,
        catigore: null,
        city: null,
        cityD: null,
        cityA: null,
        longeur: null,
        modelMachine: null,
        Machine_categorie: null,
        descreption: null,
        litre: null,
        title: null,
        phone: null,
        fullname: null,
        poids: null,
        prix: null,
        type: null,
        citys: Listcitys,
        previewImage: [],
        upImage: [],
        UrlImage: [],
        clientUID: null,
        ListType: ["À vendre", "À louer"],
        ad: null,
      };
    },
    methods: {
      
    modifier() {
      this.validation();
      if(this.errVille==false && this.errCatigory==false && this.errVilleD==false && this.errVilleA==false && this.errType==false && this.errMaciCat==false){
        var db = firebase.firestore();
      db.collection("ads")
        .where("uid", "==", this.$route.params.uid)
        .get()
        .then((querySnapshot) => {
          querySnapshot.forEach((doc) => {
            db.collection("ads")
              .doc(doc.id)
              .update({
                arrival:this.cityA,
                departure:this.cityD,
                category:this.catigore.id,
                city:this.city,
                machineCategory:this.Machine_categorie.id,
                price:this.prix,
                title:this.title,
                type:this.type,
                weight:this.poids,
                description:this.descreption,
                length:this.longeur,
                litre:this.litre,
                make:this.modelMachine,
              })
              .catch((error) => {
                this.isBuzy = false;
                console.error("Error adding document: ", error);
              });
          });
        }).then(()=>{
      db.collection("users")
        .where("uid", "==", this.clientUID)
        .get()
        .then((querySnapshot) => {
          querySnapshot.forEach((doc) => {
            db.collection("users")
              .doc(doc.id)
              .update({
                fullname:this.fullname,
                phone:this.phone,
              })
              .catch((error) => {
                this.isBuzy = false;
                console.error("Error adding document: ", error);
              });
          });
        });
        }).then(()=>{
          setTimeout(() => {
            
          location.reload();
          }, 2000);
        })
      }
    },
      async validation() {
        if (this.city && this.catigore) {
          this.errImages = false;
          this.errVille = false;
          this.errCatigory = false;
  
          if (this.catigore.id == "CBgR4EvCtyodV2EqLeKg") {
            if (this.type && this.Machine_categorie) {
              this.errMaciCat = false;
              this.errType = false;
              this.send = true;
  
              // send data
            //   this.uploudImages();
            } else {
              if (this.type == null) {
                this.errType = true;
              } else {
                this.errType = false;
              }
              if (this.Machine_categorie == null) {
                this.errMaciCat = true;
              } else {
                this.errMaciCat = false;
              }
            }
          } else if (this.catigore.id == "a3S74jwragQi3pUMwcte") {
            if (this.Machine_categorie) {
              this.errMaciCat = false;
              this.send = true;
              // send data
            //   this.uploudImages();
            } else {
              if (this.Machine_categorie == null) {
                this.errMaciCat = true;
              } else {
                this.errMaciCat = false;
              }
            }
          } else if (this.catigore.id == "rwAAHojffDZP9U4URtvN") {
            if (this.cityD && this.cityA) {
              this.errVilleA = false;
              this.errVilleD = false;
              this.send = true;
  
              // send data
            //   this.uploudImages();
            } else {
              if (this.cityD == null) {
                this.errVilleD = true;
              } else {
                this.errVilleD = false;
              }
              if (this.cityA == null) {
                this.errVilleA = true;
              } else {
                this.errVilleA = false;
              }
            }
          } else {
            this.send = true;
            // send data
            // this.uploudImages();
          }
        } else {
          if (this.city == null) {
            this.errVille = true;
          } else {
            this.errVille = false;
          }
          if (this.catigore == null) {
            this.errCatigory = true;
          } else {
            this.errCatigory = false;
          }
        }
      },
      async getData() {
      var db = firebase.firestore();
      const category= [];
      db.collection("categories")
        .get()
        .then((cat) => {
          cat.forEach((e) => {
            this.Listcatigores.push({
                id: e.data().uid, text: e.data().name
            });
          });
        });
      db.collection("machine_categories")
        .get()
        .then((cat) => {
          cat.forEach((e) => {
            this.ListMachine_categories.push({
                id: e.data().uid, text: e.data().name
            });
          });
        });
      db.collection("categories")
        .get()
        .then((user) => {
          user.forEach((doc) => {
            category.push({doc
            });
          });
          this.isBuzy = false;
        });
      db.collection("ads").where('uid','==',this.$route.params.uid)
        .get()
        .then((user) => {
          user.forEach((ad) => {
            this.ad={
                uid: ad.uid,
          title: ad.data().title,
          image: ad.data().images,
          city: ad.data().city,
          description: ad.data().description,
          price: ad.data().price,
          type: ad.data().type,
          arrival: ad.data().arrival,
          departure: ad.data().departure,
          client: ad.data().client,
          category: ad.data().category,
          Machincategory: ad.data().machineCategory,
          createdAt: new Date(ad.data().createdAt).toLocaleDateString("fr-FR"),
          status: ad.data().status,
          length: ad.data().length?ad.data().length:null ,
          make: ad.data().make,
          weight: ad.data().weight,
          litre: ad.data().litre,
            };
        });
      })
        .then(()=>{
          db.collection("categories").where('uid','==',this.ad.category)
        .get().then((categories)=>{
          categories.forEach((cat) => {
          this.catigore = {id:cat.data().uid,text:cat.data().name};
          });
        });
          db.collection("machine_categories").where('uid','==',this.ad.Machincategory)
        .get().then((categories)=>{
          categories.forEach((cat) => {
          this.Machine_categorie = {id:cat.data().uid,text:cat.data().name};
          });
        });
          db.collection("users").where('uid','==',this.ad.client)
        .get().then((users)=>{
          users.forEach((user) => {
          this.phone = user.data().phone
          this.fullname = user.data().fullname
          this.clientUID = user.data().uid
          });
        });
        this.city = this.ad.city;
        this.cityD = this.ad.departure;
        this.cityA = this.ad.arrival;
        this.longeur = this.ad.length;
        this.modelMachine = this.ad.make;
        this.descreption = this.ad.description;
        this.litre = this.ad.litre;
        this.title = this.ad.title;
        this.poids = this.ad.weight;
        this.prix = this.ad.price;
        this.type = this.ad.type;
          });
      },
    },
    mounted() {
      this.getData();
    },
  };
  </script>
  
  <style>
  .multiselect {
   margin-bottom: 22px !important;

  }
  .multiselect__tags {
  border: 1px solid rgba(230, 230, 230, 0.6) !important;
  background: rgba(230, 230, 230, 0.6)  !important;
  font-weight: 600 !important;
  font-size: 14px !important;
  color: rgba(230, 230, 230, 0.6) !important;   

}
.multiselect__input {
  background: transparent !important;
  color: rgba(230, 230, 230, 0.6)  !important;
  font-weight: 600 !important;
  font-size: 14px !important;
}
.multiselect__single {
  background: transparent !important;
  color: #999 !important;
  font-weight: 600 !important;
  font-size: 14px !important;
}

.multiselect__input::placeholder {
  color: #999 !important;
  font-weight: 600 !important;
  font-size: 14px !important;
}
.multiselect__option--highlight,
.multiselect__option--highlight:after {
  background-color: rgba(230, 230, 230, 0.6) !important;
  color: #999 !important;
  font-weight: 600 !important;
  font-size: 16px !important;
}
  .err {
    border-style: solid;
    border-color: red;
    border-radius: 10px;
    border-width: 1px;
  }
  .inputText {
    width: 100%;
    border: 2px solid rgba(0, 0, 0, 0);
    outline: none;
    background-color: rgba(230, 230, 230, 0.6);
    padding: 0.5rem 1rem;
    font-size: 1.1rem;
    margin-bottom: 22px;
    transition: 0.3s;
    border-radius: 8px;
  }
  
  .inputText:hover {
    background-color: rgba(0, 0, 0, 0.1);
  }
  .select2-container--default
    .select2-selection--single
    .select2-selection__arrow
    b {
    margin-top: 8px;
  }
  .select2-container--default .select2-selection--single {
    background-color: #fff;
    border: none;
    border-radius: 8px;
  }
  .inputText:focus {
    border: 1px solid rgb(238, 196, 106);
    background-color: rgba(230, 230, 230, 0.6);
  }
  .bt {
    background-color: rgb(240, 236, 236);
    border: none;
    width: 100%;
    height: 48px !important;
  }
  .vs__dropdown-toggle {
    padding: 8px 0 10px !important;
    align-items: center !important;
  }
  .select2-container .select2-selection--single {
    height: 48px !important;
    padding: 8px 0 10px !important;
    background-color: rgba(230, 230, 230, 0.6);
  }
  .images-upload {
    background-color: #ffffff !important;
    border-radius: 5px !important;
    border: 1px dashed #ccc !important;
    display: inline-block !important;
    cursor: pointer !important;
    width: 165px !important;
    height: 88px !important;
  }
  .images-upload:hover {
    background-color: #f1f1f1 !important;
  }
  .image-container {
    display: inline-table !important;
    height: 90px !important;
    width: 140px !important;
    display: flex !important;
  }
  .images-preview {
    border-radius: 5px !important;
    border: 1px solid rgba(230, 230, 230, 0.6) !important;
    display: inline-block !important;
    width: 140px !important;
    height: 88px !important;
    padding-top: -14px !important;
    transition: filter 0.1s linear;
  }
  .images-preview:hover {
    filter: brightness(90%);
  }
  .button-container {
    display: inline-flex !important;
    height: 90px !important;
    width: 140px !important;
    margin-right: 0.25rem !important;
    margin-left: 0.25rem !important;
  }
  .close-btn {
    background: none !important;
    color: white !important;
    border: none !important;
    padding: 0px !important;
    margin: 0px !important;
    font: inherit !important;
    cursor: pointer !important;
    outline: inherit !important;
    position: relative !important;
    right: 34px !important;
    top: -27px !important;
    width: 0px !important;
  }
  .times-icon {
    font-size: 3rem !important;
    padding: 0px !important;
    margin: 0px !important;
    filter: drop-shadow(0px 0px 1px black);
  }
  .custum-icon {
    color: #00afca !important;
    font-size: 3rem !important;
    margin-top: 24px !important;
  }
  .custum-icon:hover {
    color: #29818f !important;
  }
  </style>
  