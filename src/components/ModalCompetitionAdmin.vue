<template>
  <transition name="modal">
    <div class="modal-mask">
      <div class="modal-wrapper">
        <form class="modal-container" @submit.prevent="submitForm">
          <div class="modal-header">
            <h5>문제 관리</h5>
            <button
              type="button"
              class="btn-close"
              @click="$emit('close')"
            ></button>
          </div>

          <div class="modal-body">
              <table class = "modal-table" frame = void>
                <thead>
                  <tr>
                    <th class="col-6 competition-title" scope="col">제목</th>
                    <th class="col-5" scope="col">옵션</th>
                    <th scope="col">편집</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="(competition) in competitionList" :key="competition">
                    <td class="competition-title">{{ competition.problem.title }}</td>
                    <td scope="row" v-if="competition.problem.created_user === this.$store.state.userid">
                      <button class="edit-btn"
                              @click="showModal = true;
                                      competition_id = competition.id">
                        <font-awesome-icon icon="user-plus" />
                      </button> |
                      <ModalCompetitionTA
                        v-if="showModal"
                        @close="showModal = false"
                        :competition_id="competition_id"
                      />
                      <button class="delete-btn"
                              @click="deleteCompetition(competition.id)">
                        <font-awesome-icon icon="trash-can" />
                      </button>
                    </td>
                    <td v-else></td>
                    <td>
                      <button class="edit-btn"
                              @click="goEditCompetition(competition.id)">
                        <font-awesome-icon icon="pen" />
                      </button>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
        </form>
      </div>
    </div>
  </transition>
</template>

<script>
import ModalCompetitionTA from '@/components/ModalCompetitionTA.vue'
import api from '@/api/index.js'

export default {
  name: 'ModalCompetitionAdmin',
  components: {
    ModalCompetitionTA
  },
  data () {
    return {
      classID: this.$route.params.classID,
      competitionList: [],
      showModal: false,
      competition_id: -1,
      userCompetitionList: []
    }
  },
  created () {
    this.init()
  },
  methods: {
    init () {
      this.userInfo()
      this.getGeneralList()
    },
    async userInfo () {
      try {
        const res = await api.getUserInfo(this.$store.state.userid)
        for (var i = 0; i < res.data.competition.length; i++) {
          this.userCompetitionList.push(res.data.competition[i].competition_id)
        }
      } catch (err) {
        console.log(err)
      }
    },
    async getGeneralList () {
      try {
        this.competitionList = []
        const res = await api.getCompetitionList()
        for (var i = 0; i < res.data.length; i++) {
          if (this.userCompetitionList.includes(res.data[i].id)) {
            this.competitionList.push(res.data[i])
          }
        }
        this.competitionList.reverse()
      } catch (error) {
        console.log(error)
      }
    },
    async deleteCompetition (competitionID) {
      try {
        if (confirm('삭제하시겠습니까?')) {
          await api.deleteCompetition(competitionID)
        }
        this.getGeneralList()
      } catch (error) {
        console.log(error)
      }
    },
    goEditCompetition (competitionID) {
      this.$router.push({
        name: 'EditProblem',
        params: {
          problemType: 'general',
          problemID: competitionID
        }
      })
    }
  }
}
</script>

<style scoped lang="scss">
.modal-table {
  width: 100%;
  border-collapse: collapse;
  white-space: nowrap;
  table-layout: fixed;
  .competition-title {
    @media (max-width: 797px) {
    width: 40%;
  }
  }
  td.competition-title {
    text-overflow: ellipsis;
    overflow: hidden;
    white-space: nowrap;
  }
  tr {
    border: 1px solid black;
  }
  th {
    @media (max-width: 420px) {
      font-size: calc(0.55rem + 2vw);
    }
  }
}

tbody, td, tfoot, th, thead, tr {
  padding: 0.5rem;
}

.modal-container {
  @media (max-width: 797px) {
    width: 100%;
    max-width: initial;
    min-width: initial;
    padding-right: 20px;
    padding-left: 20px;
  }
  max-width: initial;
  min-width: initial;
  width: 700px;
  .modal-body {
    padding: 1rem 0px 0px 0px;
  }
}
</style>
