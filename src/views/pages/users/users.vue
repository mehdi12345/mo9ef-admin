<script>
import firebase from "firebase/app";
import "firebase/firestore";
import { required, minLength, sameAs } from "vuelidate/lib/validators";

/**
 * Transactions component
 */
export default {
  data() {
    return {
      users: [],
      filter: "",
      perPage: 15,
      currentPage: 1,
      buzy: false,
      modal: false,
      editmodal: false,
      fields: [
        { key: "fullname", label: "Nom du client" },
        { key: "email", label: "Address email" },
        { key: "phone", label: "Numero du telephone" },
        { key: "role", label: "Role" },
        { key: "action", label: "Action" },
      ],

      fullname: "",
      email: "",
      phone: "",
      role: "",
      password: "",
      confirmation: "",
      options: [
        { name: "Admin", item: "Admin" },
        { name: "Operator", item: "Operator" },
      ],
    };
  },
  // validation
  validations: {
    fullname: {
      required,
    },
    email: {
      required,
    },
    password: {
      required,
    },
    confirmation: {
      required,
      sameAsPassword: sameAs("password"),
    },
    phone: {
      required,
      minLength: minLength(10),
    },
    role: {
      required,
    },
  },

  computed: {
    authUser() {
      return this.$store.state.auth.currentUser;
    },
    openModal: {
      get() {
        return this.modal;
      },
      set(value) {
        this.clear();
        this.modal = value;
      },
    },
    openEditModal: {
      get() {
        return this.editmodal;
      },
      set(value) {
        this.editmodal = value;
      },
    },
    isBuzy: {
      get() {
        return this.buzy;
      },
      set(value) {
        this.buzy = value;
      },
    },

    // Total no. of records
    rows() {
      return this.users.length;
    },
  },
  mounted() {
    this.init();
    this.totalRows = this.users.length;
  },
  methods: {
    // Fetch the new ads from firebase
    async init() {
      this.isBuzy = true;
      this.users = [];
      var db = firebase.firestore();
      db.collection("admins")
        .get()
        .then((user) => {
          user.forEach((doc) => {
            this.users.push({
              id: doc.id,
              fullname: doc.data().fullname,
              email: doc.data().email,
              phone: doc.data().phone,
              role: doc.data().role,
            });
          });
          this.isBuzy = false;
        });
    },
    /**
     * Search the table data with search input
     */
    onFiltered(filteredItems) {
      // Trigger pagination to update the number of buttons/pages due to filtering
      this.totalRows = filteredItems.length;
      this.currentPage = 1;
    },
    ajouter() {
      // validate
      this.$v.$touch();
      if (this.$v.$invalid) return;
      else {
        this.isBuzy = true;
        var db = firebase.firestore();
        firebase
          .auth()
          .createUserWithEmailAndPassword(this.email, this.password)
          .then(
            () => {
              var user = firebase.auth().currentUser;
              db.collection("admins")
                .doc(user.uid)
                .set({
                  uid: user.uid,
                  fullname: this.fullname,
                  email: this.email,
                  phone: this.phone,
                  role: this.role,
                })
                .then(() => {
                  this.isBuzy = false;
                  this.modal = false;
                  this.clear();
                  this.init();
                })
                .catch((error) => {
                  this.isBuzy = false;
                  console.error("Error adding document: ", error);
                });
            },
            (error) => {
              console.log(error);
            }
          );
      }
    },
    editUser(user) {
      console.log(user);

      this.fullname = user.fullname;
      this.phone = user.phone;
      this.email = user.email;
      this.role = user.role;
      this.openEditModal = true;
    },
    modifier() {
      var db = firebase.firestore();
      db.collection("admins")
        .where("email", "==", this.email)
        .get()
        .then((querySnapshot) => {
          querySnapshot.forEach((doc) => {
            db.collection("admins")
              .doc(doc.id)
              .update({
                fullname: this.fullname,
                email: this.email,
                phone: this.phone,
                role: this.role,
              })
              .then(() => {
                this.isBuzy = false;
                this.editmodal = false;
                this.clear();
                this.init();
              })
              .catch((error) => {
                this.isBuzy = false;
                console.error("Error adding document: ", error);
              });
          });
        });
    },
    clear() {
      this.fullname = "";
      this.email = "";
      this.phone = "";
      this.role = "";
      this.password = "";
      this.confirmation = "";
    },
    supprimer(user) {
      //ckeck if user is going to  delete
      this.$bvModal
        .msgBoxConfirm("Voulez vous vraiment supprimer cet utilisateur?", {
          okTitle: "Ok",
          cancelTitle: "Annuler",
          okVariant: "danger",
          cancelVariant: "secondary",
        })
        .then((data) => {
          if (data) {
            var db = firebase.firestore();
            db.collection("admins")
              .doc(user.id)
              .delete()
              .then(() => {
                this.isBuzy = false;
                this.modal = false;
                this.clear();
                this.init();
              })
              .catch((error) => {
                this.isBuzy = false;
                console.error("Error removing document: ", error);
              });
          }
        });
    },
  },
};
</script>

<template>
  <div class="card">
    <!-- register modal -->
    <b-modal v-model="openModal" id="ajouter-modal" title="Nouveau compte">
      <form @submit.prevent="ajouter">
        <div class="form-group">
          <label for="exampleInputnom1">Nom complet</label>
          <input
            type="text"
            v-model="fullname"
            class="form-control"
            id="exampleInputnom1"
            aria-describedby="emailHelp"
            placeholder="Nom complet"
          />
          <div class="error" v-if="!$v.fullname.required">le Nom est obligatoire</div>
        </div>
        <div class="form-group">
          <label for="exampleInputEmail1">Adresse email</label>
          <input
            type="email"
            v-model="email"
            class="form-control"
            id="exampleInputEmail1"
            aria-describedby="emailHelp"
            placeholder="Adresse email"
          />
        </div>
        <div class="error" v-if="!$v.email.required">Address email est obligatoire</div>
        <div class="form-group">
          <label for="exampleInputphone1">Numero de telephone</label>
          <input
            type="text"
            v-model="phone"
            class="form-control"
            id="exampleInputphone1"
            aria-describedby="emailHelp"
            placeholder="Numero de telephone"
          />
          <div class="error" v-if="!$v.phone.required">
            le numero de telephone est obligatoire
          </div>
        </div>
        <!-- role select -->
        <div class="form-group">
          <label for="exampleFormControlSelect1">Role</label>
          <b-form-select
            v-model="role"
            :options="options"
            class="mb-3"
            value-field="item"
            text-field="name"
            disabled-field="notEnabled"
          ></b-form-select>
          <div class="error" v-if="!$v.role.required">Role est obligatoire</div>
        </div>
        <div class="form-group">
          <label for="exampleInputmotdepass1">Mot de passe</label>
          <input
            v-model="password"
            type="password"
            class="form-control"
            id="exampleInputmotdepass1"
            aria-describedby="emailHelp"
            placeholder="Mot de passe"
          />
          <div class="error" v-if="!$v.password.required">
            Mot de passe est obligatoir
          </div>
        </div>
        <div class="form-group">
          <label for="exampleInputmotdepass2">Confirmer mot de passe</label>
          <input
            v-model="confirmation"
            type="password"
            class="form-control"
            id="exampleInputmotdepass2"
            aria-describedby="emailHelp"
            placeholder="Confirmer mot de passe"
          />
          <div class="error" v-if="!$v.confirmation.sameAsPassword">
            Mot de passe et confirmation doivent etre identique
          </div>
        </div>
      </form>

      <template #modal-footer>
        <button
          type="button"
          class="btn btn-secondary"
          @click="
            openModal = false;
            clear();
          "
        >
          Fermer
        </button>
        <button type="button" @click="ajouter()" class="btn btn-primary">
          Enregistrer
        </button>
      </template>
    </b-modal>
    <!-- end register modal -->
    <!--- edit modal -->
    <b-modal v-model="openEditModal" id="modifer-modal" title="Modifier l'utilisateur">
      <form @submit.prevent="ajouter">
        <div class="form-group">
          <label for="exampleInputnom1">Nom complet</label>
          <input
            type="text"
            v-model="fullname"
            class="form-control"
            id="exampleInputnom1"
            aria-describedby="emailHelp"
            placeholder="Nom complet"
          />
          <div class="error" v-if="!$v.fullname.required">le Nom est obligatoire</div>
        </div>
        <div class="form-group">
          <label for="exampleInputphone1">Numero de telephone</label>
          <input
            type="text"
            v-model="phone"
            class="form-control"
            id="exampleInputphone1"
            aria-describedby="emailHelp"
            placeholder="Numero de telephone"
          />
          <div class="error" v-if="!$v.phone.required">
            le numero de telephone est obligatoire
          </div>
        </div>
        <!-- role select -->
        <div class="form-group">
          <label for="exampleFormControlSelect1">Role</label>
          <b-form-select
            v-model="role"
            :options="options"
            class="mb-3"
            value-field="item"
            text-field="name"
            disabled-field="notEnabled"
          ></b-form-select>
          <div class="error" v-if="!$v.role.required">Role est obligatoire</div>
        </div>
      </form>

      <template #modal-footer>
        <button type="button" class="btn btn-secondary" @click="openEditModal = false">
          Fermer
        </button>
        <button type="button" @click="modifier" class="btn btn-primary">
          Enregistrer
        </button>
      </template>
    </b-modal>
    <!-- end edit modal -->
    <div class="card-body">
      <h4 class="card-title mb-4">Utilisateurs</h4>
      <b-row align-h="between" class="mb-2">
        <b-col cols="2"
          ><div id="tickets-table_filter" class="dataTables_filter text-md-right">
            <label class="d-inline-flex align-items-center">
              Search:
              <b-form-input
                v-model="filter"
                type="search"
                class="form-control form-control-sm ml-2"
              ></b-form-input>
            </label></div
        ></b-col>
        <b-col cols="2">
          <b-button v-b-modal.ajouter-modal class="btn btn-success align-items-center">
            Ajoutez un utilisateurs
          </b-button></b-col
        >
        <!-- Search -->

        <!-- End search -->
      </b-row>
      <div class="table-responsive">
        <b-table
          :items="users"
          :fields="fields"
          responsive="sm"
          :per-page="perPage"
          :current-page="currentPage"
          :filter="filter"
          :busy.sync="isBuzy"
          show-empty
          @filtered="onFiltered"
          empty-text="Aucune client trouvée"
          empty-filtered-text="Aucune client trouvée"
        >
          <template #table-busy>
            <div class="text-center text-primary my-2">
              <b-spinner class="align-middle"></b-spinner>
              <strong> Loading...</strong>
            </div>
          </template>
          <template #empty="scope">
            <div class="text-center">
              <h6 class="align-middle">{{ scope.emptyText }}</h6>
            </div>
          </template>
          <template v-slot:cell(role)="row">
            <div
              class="badge font-size-12"
              :class="{
                'badge-soft-danger': `${row.item.role}` === 'Admin',
                'badge-soft-warning': `${row.item.role}` === 'Operator',
              }"
            >
              {{ row.value }}
            </div>
          </template>
          <template v-slot:cell(action)="row">
            <b-button
              size="sm"
              variant="outline-primary"
              @click="
                openEditModal = true;
                editUser(row.item);
              "
              class="mr-2"
            >
              Modifier
            </b-button>
            <b-button size="sm" variant="outline-danger" @click="supprimer(row.item)">
              Supprimer
            </b-button>
          </template>
        </b-table>
      </div>
      <div class="row">
        <div class="col">
          <div class="dataTables_paginate paging_simple_numbers float-right">
            <ul class="pagination pagination-rounded mb-0">
              <!-- pagination -->
              <b-pagination
                v-model="currentPage"
                :total-rows="rows"
                :per-page="perPage"
              ></b-pagination>
            </ul>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
