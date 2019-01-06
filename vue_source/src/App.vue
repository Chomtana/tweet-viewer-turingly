<template>
  <div :class="['theme-'+theme]">
    <!-- Navbar -->
    <nav class="navbar fixed-top navbar-expand-lg navbar-light bg-light">
      <a class="navbar-brand">Tweet Viewer</a>
      <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
        <span class="navbar-toggler-icon"></span>
      </button>

      <div class="collapse navbar-collapse" id="navbarSupportedContent">
        <ul class="navbar-nav ml-auto">
          <li class="nav-item active">
            <a class="nav-link" href="javascript:;" onclick='$("#change-theme-modal").modal("show")'>Change theme</a>
          </li>
          <li class="nav-item">
            <a class="nav-link" href="javascript:;" @click="toggleEditMode">{{editMode?"Finish edit layout":"Edit layout"}}</a>
          </li>
          <li class="nav-item active">
            <a class="nav-link" href="javascript:;" onclick='$("#add-user-modal").modal("show")'>Add user</a>
          </li>
        </ul>
      </div>
    </nav>

    <!-- App -->
    <div id="app" :class="['container-fluid']">
      <center><h1 v-if="editMode" class="editing-text">Edit Mode</h1></center>
      <div class="row">
        <div class="col-12 col-md-6 col-lg-4" v-for="(user,i) in users" :key="user">
          <h2 class="colhead"><a :href="'https://twitter.com/'+user" target="_blank">@{{user}}</a></h2>
          <TweetViewer :user="user" :editMode="editMode" @toLeft="toLeft(i)" @toRight="toRight(i)" @doRemove="doRemove(i)"/>
        </div>
      </div>
    </div>

    <!-- Change theme modal -->
    <div class="modal fade" id="change-theme-modal" tabindex="-1" role="dialog" aria-hidden="true">
      <div class="modal-dialog" role="document">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title" id="exampleModalLabel">Change theme</h5>
            <button type="button" class="close" data-dismiss="modal" aria-label="Close">
              <span aria-hidden="true">&times;</span>
            </button>
          </div>
          <div class="modal-body">
            <div class="row align-items-center">
              <div class="col-3 col-md-2">Theme</div>
              <div class="col-9 col-md-10">
                <select v-model="theme" class="form-control" @change="savetheme">
                  <option value="deep-blue">Deep Blue</option>
                  <option value="dark">Dark</option>
                  <option value="light">Light</option>
                </select>
              </div>
            </div>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
          </div>
        </div>
      </div>
    </div>

    <!-- Add user modal -->
    <div class="modal fade" id="add-user-modal" tabindex="-1" role="dialog" aria-hidden="true">
      <div class="modal-dialog" role="document">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title">Add user</h5>
            <button type="button" class="close" data-dismiss="modal" aria-label="Close">
              <span aria-hidden="true">&times;</span>
            </button>
          </div>
          <div class="modal-body">
            <div class="row align-items-center">
              <div class="col-3 col-md-2">Username</div>
              <div class="col-9 col-md-10">
                <input id="add-user-input" placeholder="@Chomtana" class="form-control">
              </div>
            </div>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
            <button type="button" class="btn btn-success" data-dismiss="modal" @click="addUser">Add user</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import TweetViewer from './components/TweetViewer.vue'
import Vue from 'vue'; 

var theme = localStorage.getItem("theme");
var users = localStorage.getItem("users");
if (!theme) {
  theme = "deep-blue";
  localStorage.setItem("theme",theme);
}
if (!users) {
  users = ["MakeSchool","newsycombinator","ycombinator"];
  localStorage.setItem("users",users);
} else {
  users = users.split(",")
}

export default {
  name: 'app',
  components: {
    TweetViewer
  },
  data() {
    return {
      users: users,
      theme: theme,
      editMode: false
    }
  },
  methods: {
    savetheme() {
      localStorage.setItem("theme",this.theme);
    },
    toggleEditMode() {
      this.editMode = !this.editMode;
    },
    toLeft(i) {
      if (i>0) {
        var temp = this.users[i-1];
        Vue.set(this.users,i-1,this.users[i]);
        Vue.set(this.users,i,temp);
      }
      localStorage.setItem("users",this.users);
    },
    toRight(i) {
      if (i<this.users.length-1) {
        var temp = this.users[i+1];
        Vue.set(this.users,i+1,this.users[i]);
        Vue.set(this.users,i,temp);
      }
      localStorage.setItem("users",this.users);
    },
    doRemove(i) {
      this.users.splice(i,1);
      localStorage.setItem("users",this.users);
    },
    addUser() {
      var username = $("#add-user-input").val();
      if (username.startsWith('@')) {
        username = username.substr(1);
      }
      this.users.push(username);
      localStorage.setItem("users",this.users);
      $("#add-user-input").val("");
    }
  }
}
</script>

<style lang="scss">
html,body {
  padding: 0;
  margin: 0;
  overflow-x: hidden;
}

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 55px;
  padding: 10px;
}

.theme-deep-blue {
  #app {
    background-color:darkblue;
  }

  nav.navbar {
    background-color: #329cc3 !important;
    color: white !important;
    a, li, div {
      color: white !important;
    }

    .navbar-toggler {
      border-color: white;
      .navbar-toggler-icon {
        background-image: url("data:image/svg+xml;charset=utf8,%3Csvg viewBox='0 0 32 32' xmlns='http://www.w3.org/2000/svg'%3E%3Cpath stroke='rgba(255,255,255,1)' stroke-width='2' stroke-linecap='round' stroke-miterlimit='10' d='M4 8h24M4 16h24M4 24h24'/%3E%3C/svg%3E");
        
      }
    }
  }

  .colhead, .editing-text {
    &, & a{
      color: white;
    }
  }
}

.theme-dark {
  #app {
    background-color:black;
  }

  nav.navbar {
    background-color: #666 !important;
    color: white !important;
    a, li, div {
      color: white !important;
    }

    .navbar-toggler {
      border-color: white;
      .navbar-toggler-icon {
        background-image: url("data:image/svg+xml;charset=utf8,%3Csvg viewBox='0 0 32 32' xmlns='http://www.w3.org/2000/svg'%3E%3Cpath stroke='rgba(255,255,255,1)' stroke-width='2' stroke-linecap='round' stroke-miterlimit='10' d='M4 8h24M4 16h24M4 24h24'/%3E%3C/svg%3E");
        
      }
    }
  }

  .card-body {
    background-color: #666;
    color: white;

    a {
      color: greenyellow;
    }

    .card-subtitle {
      color: #ddd !important;
    }
  }

  .colhead, .editing-text {
    &, & a{
      color: white;
    }
  }
}

.theme-light {
  nav.navbar {
    background-color: #eee !important;
    .nav-link{
      color: black;
    }
  }

  .colhead, .editing-text {
    &, & a{
      color: black;
    }
  }
}

.nav-item:hover {
  opacity: 0.8;
}
</style>
