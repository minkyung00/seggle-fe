<template>
  <div class="container">
    <header>
      <h1 id="title">수업 및 시험</h1>
      <button class="btn" @click="editClassList">저장</button>
    </header>

    <table class="table">
      <thead>
        <tr>
          <th scope="col">
            <font-awesome-icon icon="check" />
          </th>
          <th scope="col">학기</th>
          <th scope="col">제목</th>
          <th scope="col">편집</th>
          <th scope="col">삭제</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(classes, i) in classList" :key="i">
          <th scope="row">
            <input
              class="form-check-input"
              type="checkbox"
              :value="classes.id"
              v-model="checkList"
            />
          </th>
          <td>{{ classes.semester }}</td>
          <td class="class-name">
            <p>{{ classes.name }}</p>
          </td>
          <td>
            <button class="edit-btn"
                    @click="showModal = true;
                            rowIndex = i;">
              <font-awesome-icon icon="pen" />
            </button>
            <ModalClassList
              v-if="showModal"
              @close="showModal = false"
              mode="수업 편집"
              :classID="classList[rowIndex].id"
              :semester="classList[rowIndex].semester"
              :title="classList[rowIndex].name"
            />
          </td>
          <td>
            <button class="delete-btn"
                    @click="removeClass(classes.id)">
              <font-awesome-icon icon="trash-can" />
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import api from '@/api/index.js'
import ModalClassList from '@/components/ModalClassList.vue'
export default {
  name: 'EditClassList',
  components: {
    ModalClassList
  },
  data () {
    return {
      userID: this.$store.state.userid,
      classList: [],
      checkList: [],
      showModal: false,
      rowIndex: ''
    }
  },
  mounted () {
    this.getClassList()
  },
  methods: {
    async getClassList () {
      try {
        const res = await api.getClassList()
        this.classList = res.data
        this.alreadyChecked()
      } catch (err) {
        console.log(err)
      }
    },
    alreadyChecked () {
      // is_show이면 체크되어있어야함
      for (let i = 0; i < this.classList.length; i++) {
        if (this.classList[i].is_show) {
          this.checkList.push(this.classList[i].id)
        }
      }
    },
    async editClassList () {
      try {
        const data = []
        for (let i = 0; i < this.checkList.length; i++) {
          const item = {}
          item.class_id = this.checkList[i]
          data.push(item)
        }
        await api.editClassList(data)
        alert('변경사항이 저장되었습니다.')
        this.$router.push({ name: 'ClassList' })
      } catch (err) {
        console.log(err)
      }
    },
    async removeClass (classID) {
      try {
        if (confirm('삭제하시겠습니까?')) {
          await api.removeClass(classID)
          alert('변경사항이 저장되었습니다.')
          this.$router.push({ name: 'ClassList' })
        }
      } catch (err) {
        console.log(err)
      }
    }
  }
}
</script>

<style scoped lang="scss">
.container {
  padding: 3rem 3rem;
  @media (max-width: 420px) {
    padding: 1rem 1rem;
  }

  header {
    display: flex;
    justify-content: space-between;
    padding: 3rem 0rem;
    @media (max-width: 420px) {
      display: block;
    }

    h1 {
      margin-bottom: 0;
    }

    .btn {
      width: 6rem;
      @media (max-width: 420px) {
        font-size: 14px;
      }
    }
  }
  .table {
    // text-align: left;
    p {
      margin-bottom: 0;
      @media (max-width: 420px) {
        display: block;
        width: 80px;
        overflow: hidden;
        text-overflow: ellipsis;
        white-space: nowrap;
      }
    }
  }

  .table > :not(caption) > * > * {
    @media (max-width: 420px) {
      padding: 0.5rem 0.25rem;
    }
  }
}
</style>
