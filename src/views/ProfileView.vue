<script>
import PersonalInfoCard from '../components/PersonalInfoCard.vue'
import SkillsView from '../views/SkillsView.vue'

export default {
  inject: ['loggedInPerson', 'personService'],

  mounted() {
    this.profileOf = this.$route.query.profileOf
    this.refreshPersonalInfo(this.profileOf)
  },

  data() {
    return {
      route: '',
      personalInfo: {},
      profileOf: '',
      fieldsArray: this.loggedInPerson.personalInfo,
      unsavedChanges: false,
    }
  },

  computed: {
    isMyProfile() {
      return this.loggedInPerson.email === this.profileOf
    }
  },

  methods: {
    updateChildData(updatedData, index) {
      this.fieldsArray[`${index}`] = updatedData
      this.saveChanges()
    },

    saveChanges() {
      this.personService.updatePersonalInfo(this.profileOf, this.fieldsArray)
        .then(response => {
          this.refreshPersonalInfo(this.profileOf)
        })
    },

    refreshPersonalInfo(email) {
      this.personService.getPersonalInfo(email)
        .then(response => {
          this.personalInfo = response.data
          this.fieldsArray = this.personalInfo
        })
    },

    experiencesUpdated(){
      this.refreshPersonalInfo(this.loggedInPerson.email)
    }

  },
  watch: {
    $route(to, from) {
      if (to.query.profileOf !== from.query.profileOf) {
        this.refreshPersonalInfo(to.query.profileOf);
      }
    }
  },

  components: { PersonalInfoCard, SkillsView }

}
</script>

<template>
  <main>
    <div class="">
      <div v-if="profileOf === this.loggedInPerson.email">
        <div class="row align-items-center">
          <div class="title">
            <h4>My Profile</h4>
          </div>

          <button class="col-2 btn btn-warning" v-if="unsavedChanges" @click="saveChanges()">Save Changes</button>

        </div>

      </div>
      <div v-else>
        <div class="row align-items-center">

          <h4 class="col-8 myProfileHeader">Colleague</h4>

        </div>
      </div>
    </div>

    <div class="personalInfoCards">
      <PersonalInfoCard v-if="personalInfo.firstName !== undefined" class="personalInfoField firstName"
        :fieldValue="personalInfo.firstName" :fieldName="'firstName'" :fieldNameDisplay="'First Name'" :key="'firstName'"
        :isMyProfileProp="isMyProfile" @updateData="updateChildData" />

      <PersonalInfoCard v-if="personalInfo.lastName !== undefined" class="personalInfoField lastName"
        :fieldValue="personalInfo.lastName" :fieldName="'lastName'" :fieldNameDisplay="'Last Name'" :key="'lastName'"
        :isMyProfileProp="isMyProfile" @updateData="updateChildData" />

      <PersonalInfoCard v-if="personalInfo.availableDaysPerWeek !== undefined"
        class="personalInfoField availableDaysPerWeek" :fieldValue="personalInfo.availableDaysPerWeek"
        :fieldName="'availableDaysPerWeek'" :fieldType="'combobox'"
        :fieldNameDisplay="'Available number of days per week'" :key="'availableDaysPerWeek'"
        :isMyProfileProp="isMyProfile" @updateData="updateChildData" />

      <PersonalInfoCard v-if="personalInfo.jobTitle !== undefined" class="personalInfoField jobTitle"
        :fieldValue="personalInfo.jobTitle" :fieldName="'jobTitle'" :fieldNameDisplay="'Job Title'" :key="'jobTitle'"
        :isMyProfileProp="isMyProfile" @updateData="updateChildData" />

      <PersonalInfoCard v-if="personalInfo.branch !== undefined" class="personalInfoField branch"
        :fieldValue="personalInfo.branch" :fieldName="'branch'" :fieldNameDisplay="'Located branch'" :key="'branch'"
        :fieldType="'combobox'" :isMyProfileProp="isMyProfile" @updateData="updateChildData" />

      <PersonalInfoCard v-if="personalInfo.biography !== undefined" class="personalInfoField biography"
        :fieldValue="personalInfo.biography" :fieldName="'biography'" :fieldType="'textarea'"
        :fieldNameDisplay="'Biography'" :key="'biography'" :isMyProfileProp="isMyProfile" @updateData="updateChildData" />

      <PersonalInfoCard v-if="personalInfo.experiences !== undefined" class="personalInfoField experiences"
        :fieldValue="personalInfo.experiences" :fieldName="'experiences'" :fieldType="'experiences'"
        :fieldNameDisplay="'Experiences'" :isMyProfileProp="isMyProfile"
        @updateData="this.refreshPersonalInfo(this.loggedInPerson.email)" />
    </div>

    <div v-if="profileOf !== this.loggedInPerson.email">
      <SkillsView :skillsOfProp="this.$route.query.profileOf">

      </SkillsView>
    </div>

  </main>
</template>

<style>
@media (min-width: 1024px) {
  .about {
    display: flex;
    align-items: center;
  }
}

.personalInfoCards {
  margin-top: 2%;
}

.header {
  margin-top: 1%;
  margin-bottom: 1%;
}

.myProfileHeader {
  display: inline-block;
}
</style>
