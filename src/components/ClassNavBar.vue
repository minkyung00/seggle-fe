<template>
  <div class="class-nav-bar py-4">
    <nav class="navbar navbar-expand-lg">
      <div class="container">
        <button
          class="navbar-toggler"
          type="button"
          data-bs-toggle="collapse"
          data-bs-target="#navbarNav"
          aria-controls="navbarNav"
          aria-expanded="false"
          aria-label="Toggle navigation"
        >
          <div class="navbar-toggler-icon">
            <font-awesome-icon icon="bars" />
          </div>
        </button>
        <div class="collapse navbar-collapse" id="navbarNav">
          <!-- Pills navs -->
          <ul class="nav nav-pills mb-2 mb-lg-0" role="tablist">
            <li class="nav-item">
              <a
                class="nav-link"
                id="ClassAllProblem"
                data-bs-toggle="pill"
                role="tab"
                @click="handleRoute('ClassAllProblem')"
                >전체 문제</a
              >
            </li>
            <li class="nav-item">
              <a
                class="nav-link"
                id="ClassStudentManage"
                data-bs-toggle="pill"
                role="tab"
                @click="handleRoute('ClassStudentManage')"
                >수강생 및 TA 관리</a
              >
            </li>
            <li class="nav-item">
              <a
                class="nav-link"
                id="ClassProblem"
                data-bs-toggle="pill"
                role="tab"
                @click="handleRoute('ClassProblem')"
                >문제</a
              >
            </li>
          </ul>
          <!-- Pills navs -->
        </div>
      </div>
    </nav>
  </div>
</template>

<script>
export default {
  name: 'ClassNavBar',
  props: {
    className: String
  },
  mounted () {},
  methods: {
    handleRoute (route) {
      this.$router.push({
        name: route,
        params: this.$route.params.classID
      })
    }
  }
}
</script>

<style lang="scss" scoped>
h1 {
  padding: 0px 0rem;
  margin-bottom: 0rem;

  display: block;
  max-width: 300px;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;

  @media (max-width: 992px) {
    width: 50%;
    padding: 0rem;
  }
}
.navbar-toggler {
  &:focus {
    box-shadow: 0 0 0 0rem;
  }
}

#navbarNav {
  justify-content: space-evenly;
  font-weight: bold;

  ul {
    @media (max-width: 992px) {
      margin-top: 20px;
      display: block;
    }
  }
  .nav-link {
    border-radius: 30px;
    // background-color: #060c20;
  }
  .active {
    background-color: #060c20;
  }
  .nav-item {
    padding: 0.5rem 1.5rem;

    // &:hover {
    //   background-color: var(--bs-light);
    // }
  }
}
</style>
