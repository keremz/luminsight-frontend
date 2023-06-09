<script setup>
import SkillCard from '../components/SkillCard.vue'
import ManageSkillModal from '../components/ManageSkillModal.vue'
import { PersonService } from '../services/PersonService';
import { reactive } from 'vue';
</script>

<script>

export default {
  inject: ['loggedInPerson', 'personService'],

  props: {
    skillsOfProp: {
      default: ''
    }
  },

  mounted() {
    if (this.skillsOfProp !== '') {
      this.skillsOf = this.skillsOfProp
    } else {
      this.skillsOf = this.$route.query.skillsOf

    }
    this.refreshSkills()
  },

  data() {
    return {
      skillsOf: '',
      skills: [],
      editingSkill: {},
      modalOpened: false
    }
  },

  computed: {
    skillsComputed() {
      return this.skills
    },

    isMyProfile() {
      return this.loggedInPerson.email === this.skillsOf
    }
  },

  methods: {
    toggleModal() {
      this.modalOpened = !this.modalOpened
    },

    refreshSkills() {
      this.personService.getPersonsSkills(this.skillsOf)
        .then(response => {
          this.skills = response.data
          this.sortSkillsAlphabetically()
        })
    },

    filterSkills() {
      this.personService.getSkillsStartingWith(this.skillsOf, this.searchText)
        .then(response => {
          this.skills = response.data
          this.sortSkillsAlphabetically()
        })
    },

    skillsUpdated() {
      this.refreshSkills()
    },

    sortSkillsAlphabetically() {
      this.skills.sort((skillA, skillB) => skillA.skillName.localeCompare(skillB.skillName));
    }
  },

}
</script>

<template>
  <main>
    <div class="title">
      <h4>{{ isMyProfile ? 'My Skills' : `` }}</h4>
    </div>
    <div v-if="isMyProfile">

      <div>
        <button class="btn  add-skill" @click="toggleModal()">Add skill</button>



        <ManageSkillModal ref="manageSkillModal" v-if="modalOpened" :skillNameProp="''" :starsProp="0" :open="true"
          @close="modalOpened = false" @skills-updated="skillsUpdated()">

          <template #title>
            Add a Skill
          </template>

          <template #rightButton>
            <button @click="$refs.manageSkillModal.addSkill()" class="btn btn-warning save">Add Skill</button>
          </template>
        </ManageSkillModal>
      </div>

    </div>

    <div class="search-skill">
      <input type="text" class="form-control" v-model="searchText" @input="filterSkills" @keydown.enter="addTag"
        placeholder="Search skills..." />
    </div>

    <SkillCard class="skill-card" v-for="skill in skillsComputed" :key="skill.skillName" :skillName='skill.skillName'
      :stars='skill.proficiency' @skills-updated="refreshSkills()" :isMyProfile="isMyProfile">
    </SkillCard>

  </main>
</template>

<style>
@media (min-width: 1024px) {
  .about {
    display: flex;
    align-items: center;
  }
}

.title {
  margin-top: 2%
}

.add-skill {
  margin-top: 2%;
  margin-bottom: 2%;
  background-color: purple;
  color: white;
}

.add-skill:hover {
  background-color: purple;
  color: white;
  box-shadow: 0rem 0rem 1rem purple;
}

.search-skill {
  margin-bottom: 2%;
  border-radius: 11%;
}

.skill-card {
  display: inline-block;
  margin-right: 3rem;
  margin-bottom: 2%;
  width: 15rem;
}
</style>
