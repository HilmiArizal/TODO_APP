<template>
  <div class="body-todo">
    <div class="title-body">TODO APP WITH VUE FRAMEWORK !</div>
    <img src="../assets/logo.png" alt="logo-vue" class="logo-vue" />
    <hr style="color: white" />
    <div class="title-form-todo">TODO APP</div>
    <div class="form-todo">
      <div>
        <input
          type="text"
          class="form-control input-todo"
          placeholder="Nama lengkap"
          v-model="name"
          @keyup.enter="addTodo"
        />
      </div>
      <div>
        <input
          type="text"
          class="form-control input-todo"
          placeholder="Nama aktifitas"
          v-model="activity"
          @keyup.enter="addTodo"
        />
      </div>
      <div>
        <select class="form-control select-day-todo" v-model="day">
          <option value="" disabled>--Pilih hari--</option>
          <option value="Senin">Senin</option>
          <option value="Selasa">Selasa</option>
          <option value="Rabu">Rabu</option>
          <option value="Kamis">Kamis</option>
          <option value="Jumat">Jum'at</option>
          <option value="Sabtu">Sabtu</option>
          <option value="Minggu">Minggu</option>
        </select>
      </div>
      <div class="input-time">
        <input
          type="number"
          class="form-control input-todo"
          placeholder="Dari jam"
          v-model="time"
          @keyup.enter="addTodo"
        />
        <div class="until-input-time">-</div>
        <input
          type="number"
          class="form-control input-todo"
          placeholder="Sampai jam"
          v-model="time2"
          @keyup.enter="addTodo"
        />
      </div>
      <div>
        <input
          type="date"
          class="form-control input-todo"
          placeholder="Masukan aktifitas anda"
          v-model="date"
          @keyup.enter="addTodo"
        />
      </div>
    </div>
    <div>
      <button class="btn btn-primary button-todo" @click="addTodo">
        SUBMIT
      </button>
    </div>
    <div class="results-todo">
      <div class="title-results-todo">AKTIFITAS ANDA</div>
      <table class="table table-sm">
        <thead>
          <tr class="head-results-todo">
            <th scope="col">No</th>
            <th scope="col">Nama</th>
            <th scope="col">Aktifitas</th>
            <th scope="col">Hari Aktifitas</th>
            <th scope="col">Jam Aktifitas</th>
            <th scope="col">Tanggal Aktifitas</th>
            <th scope="col">Aksi</th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="(item, index) in dataTodo"
            :key="item.idtodo"
            class="body-results-todo"
          >
            <th scope="row">
              {{ index + 1 }}
            </th>
            <td scope="row" v-if="selectedEdit === index">
              <input
                type="text"
                :placeholder="[[item.name]]"
                class="input-edit-todo"
                v-model="editName"
                @keyup.enter="confirmEdit(item.idtodo)"
              />
            </td>
            <td scope="row" v-else>
              {{ item.name }}
            </td>
            <td scope="row" v-if="selectedEdit === index">
              <input
                type="text"
                :placeholder="[[item.activity]]"
                class="input-edit-todo"
                v-model="editActivity"
                @keyup.enter="confirmEdit(item.idtodo)"
              />
            </td>
            <td scope="row" v-else>
              {{ item.activity }}
            </td>
            <td
              scope="row"
              v-if="selectedEdit === index"
              @keyup.enter="confirmEdit(item.idtodo)"
            >
              <select v-model="editDay">
                <option value="" disabled>--Pilih hari--</option>
                <option value="Senin">Senin</option>
                <option value="Selasa">Selasa</option>
                <option value="Rabu">Rabu</option>
                <option value="Kamis">Kamis</option>
                <option value="Jumat">Jum'at</option>
                <option value="Sabtu">Sabtu</option>
                <option value="Minggu">Minggu</option>
              </select>
            </td>
            <td scope="row" v-else>
              {{ item.day }}
            </td>
            <td scope="row" v-if="selectedEdit === index">
              <input
                type="number"
                placeholder="Dari jam"
                class="input-edit-todo"
                v-model="editTime"
                @keyup.enter="confirmEdit(item.idtodo)"
              />
              -
              <input
                type="number"
                placeholder="Sampai jam"
                class="input-edit-todo"
                v-model="editTime2"
                @keyup.enter="confirmEdit(item.idtodo)"
              />
            </td>
            <td scope="row" v-else>
              {{ item.time }}
            </td>
            <td scope="row" v-if="selectedEdit === index">
              <input
                type="date"
                :placeholder="[[item.date]]"
                class="input-edit-todo"
                v-model="editDate"
                @keyup.enter="confirmEdit(item.idtodo)"
              />
            </td>
            <td scope="row" v-else>
              {{ item.date }}
            </td>
            <td class="aksi-todo-width">
              <div class="aksi-results-todo" v-if="selectedEdit === index">
                <div class="delete-todo" @click="cancelEdit()">Batal</div>
                <div class="edit-todo" @click="confirmEdit(item.idtodo)">
                  Simpan
                </div>
              </div>
              <div class="aksi-results-todo" v-else>
                <div class="edit-todo" @click="editTodo(index)">Ubah</div>
                <div
                  class="delete-todo"
                  @click="deleteTodo(item.idtodo, item.name, item.activity)"
                >
                  Hapus
                </div>
              </div>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import { API_URL } from "../helpers/index";

export default {
  data() {
    return {
      name: "",
      activity: "",
      day: "",
      time: "",
      time2: "",
      date: "",

      editName: "",
      editActivity: "",
      editDay: "",
      editTime: "",
      editTime2: "",
      editDate: "",

      dataTodo: [],

      selectedEdit: null,
    };
  },
  created: function () {
    this.getTodo();
  },
  methods: {
    async getTodo() {
      const res = await axios.get(API_URL + `todo/getTodo`);
      this.dataTodo = res.data;
    },
    async addTodo() {
      if (
        this.name === "" ||
        this.activity === "" ||
        this.day === "" ||
        this.time === "" ||
        this.time2 === "" ||
        this.date === ""
      ) {
        alert("Tidak boleh kosong!");
      } else {
        let name = this.name;
        let activity = this.activity;
        let day = this.day;
        let time = this.time + " s/d " + this.time2;
        let date = this.date;
        let dataTodo = {
          name,
          activity,
          day,
          time,
          date,
        };
        console.log(dataTodo);
        await axios.post(API_URL + `todo/addTodo`, dataTodo);
        (this.name = ""),
          (this.activity = ""),
          (this.day = ""),
          (this.time = ""),
          (this.time2 = ""),
          (this.date = "");
        this.getTodo();
      }
    },
    async deleteTodo(idtodo, name, activity) {
      let upperName = name.charAt(0).toUpperCase();
      let lowerName = name.substring(1);
      let completeName = upperName + lowerName;
      if (
        window.confirm(
          `${completeName} yakin menghapus aktivitas ${activity} ?`
        )
      ) {
        await axios.delete(API_URL + `todo/deleteTodo?idtodo=${idtodo}`);
        this.getTodo();
      }
    },
    editTodo(index) {
      this.selectedEdit = index;
    },
    cancelEdit() {
      this.selectedEdit = null;
    },
    async confirmEdit(idtodo) {
      let name = this.editName;
      let activity = this.editActivity;
      let day = this.editDay;
      let time = this.editTime + " s/d " + this.editTime2;
      let date = this.editDate;
      let dataTodo = {
        name,
        activity,
        day,
        time,
        date,
      };
      await axios.patch(API_URL + `todo/editTodo?idtodo=${idtodo}`, dataTodo);
      (this.editName = ""),
        (this.editActivity = ""),
        (this.editDay = ""),
        (this.editTime = ""),
        (this.editTime2 = ""),
        (this.editDate = ""),
        (this.selectedEdit = null);
      this.getTodo();
    },
  },
};
</script>

<style>
.body-todo {
  background-image: url("../assets/vespa-bg.jpg");
  background-size: cover;
  background-repeat: no-repeat;
  background-attachment: fixed;
  min-height: 100vh;
  padding: 2em;
}
.title-body {
  font-size: 200%;
  font-weight: bold;
  text-align: center;
  padding-top: 1%;
  color: #ffffff;
  text-shadow: 0px 0px 5px rgb(160, 160, 160);
}
.logo-vue {
  width: 10%;
  display: flex;
  margin: auto;
}
.form-todo {
  display: flex;
  margin: 2% auto;
  justify-content: space-between;
}
.title-form-todo {
  text-align: center;
  font-size: 130%;
  font-weight: bold;
  color: #ffffff;
}
.form-todo .input-todo {
  font-size: 80%;
  margin: auto;
}
.input-time {
  display: flex;
  width: 18%;
}
.until-input-time {
  color: #ffffff;
  margin: 0 5px;
}
.button-todo {
  font-size: 80%;
  width: 20%;
  margin: auto;
  display: flex;
  justify-content: center;
}
.results-todo {
  background-color: #ffffff;
  padding: 2em;
  /* width: 50%; */
  margin: 2% auto;
}
.title-results-todo {
  text-align: center;
  font-size: 120%;
  font-weight: bold;
  margin-bottom: 1%;
}
.head-results-todo {
  font-size: 80%;
  text-align: center;
}
.body-results-todo {
  text-align: center;
}
.body-results-todo td {
  font-size: 80%;
  /* width: 30%; */
}
.body-results-todo th {
  font-size: 80%;
}
.aksi-results-todo {
  display: flex;
  justify-content: space-around;
}
.aksi-todo-width {
  width: 20%;
}
.edit-todo {
  background-color: rgb(77, 77, 255);
  width: 30%;
  border-radius: 3px;
  color: #ffffff;
  cursor: pointer;
}
.delete-todo {
  background-color: rgb(255, 77, 77);
  width: 30%;
  border-radius: 3px;
  color: #ffffff;
  cursor: pointer;
}
.input-edit-todo {
  font-size: 80%;
}
.select-day-todo {
  font-size: 80%;
  width: 150%;
}
@media (min-width: 300px) and (max-width: 700px) {
  .results-todo {
    width: 100%;
  }
  .edit-todo {
    width: 70%;
  }
  .delete-todo {
    width: 70%;
  }
}
@media (min-width: 701px) and (max-width: 1000px) {
  .results-todo {
    width: 80%;
  }
  .edit-todo {
    width: 40%;
  }
  .delete-todo {
    width: 40%;
  }
}
</style>
