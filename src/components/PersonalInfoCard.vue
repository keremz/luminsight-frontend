<script>
import Experience from './Experience.vue'
import ExperienceModal from './ExperienceModal.vue'

export default {
    props: {
        fieldValue: {

        },
        fieldName: {

        },
        fieldNameDisplay: {

        },
        fieldType: {
            default: 'input'
        },
        isMyProfileProp: {

        }

    },

    data() {
        return {
            clicked: false,
            field: this.fieldName,
            value: this.fieldValue,
            valueType: typeof (this.fieldValue),
            branchOptions: ['Rotterdam', 'Amsterdam', 'Apeldoorn', 'Arnhem'],
            availableDaysPerWeekOptions: [0, 1, 2, 3, 4, 5],
            experienceModalOpened: false
        }
    },

    methods: {
        notifyParent() {
            this.$emit('updateData', this.value, this.field);
        },

        setClicked(state) {
            if (this.isMyProfileProp) {
                this.clicked = state
                if (this.clicked === true) {
                    this.$nextTick(() => {
                        this.$refs.inputElement.focus();
                    });
                }
                this.notifyParent()
            }
        },

    },

    watch: {
        value: {
            deep: true,
            handler() {
                // Call the notifyParent method when any changes occur in the data object
                this.notifyParent();
            }
        }
    },

    components: {
        Experience, ExperienceModal
    }
}
</script>

<template>
    <div>

        <div v-if="this.valueType === 'string' || this.valueType === 'number'">
            <div class="fieldNameContainer">
                <p class="fieldName">{{ fieldNameDisplay }}:</p>
            </div>

            <div class="fieldValue">
                <div v-if="clicked">
                    <div v-if="fieldType === 'combobox'">
                        <select ref="inputElement" @keyup.enter="setClicked(false)" @blur="setClicked(false)"
                            v-model="value">
                            <option v-if="fieldName === 'branch'" v-for="option in branchOptions" :value="option">{{ option
                            }}
                            </option>
                            <option v-if="fieldName === 'availableDaysPerWeek'"
                                v-for="option in availableDaysPerWeekOptions" :value="option">{{ option }}</option>
                        </select>
                    </div>
                    <div v-if="fieldType === 'textarea'">
                        <textarea class="form-control" rows="2" cols="100" ref="inputElement" @blur="setClicked(false)"
                            v-model="value" type="text"></textarea>
                    </div>
                    <div v-if="fieldType === 'input'">

                        <input ref="inputElement" @keyup.enter="setClicked(false)" @blur="setClicked(false)" v-model="value"
                            type="text">
                    </div>
                </div>

                <div v-else>
                    <div class="value-container" v-if="isMyProfileProp" @click="setClicked(true)">

                        <div class="value">
                            {{ value }}
                        </div>
                    </div>
                    <div class="colleague-value" v-else @click="setClicked(true)">
                        <div class="value">
                            {{ value }}
                        </div>
                    </div>

                </div>


            </div>
        </div>

        <div @click=" " v-if="this.fieldType === 'experiences'">
            <div class="fieldNameContainerExperiences">
                <p class="fieldNameExperiences">{{ fieldNameDisplay }}</p>
                <div class="add-experience-btn-container" v-if="this.isMyProfileProp">
                    <button class="btn add-experience-btn" @click="experienceModalOpened = true">
                        Add Experience
                    </button>
                </div>
            </div>
            <div v-for="experience in fieldValue" :key="experience.title" class="experience">
                <Experience :experience="experience" @experienceUpdated="notifyParent()" />
            </div>
            <ExperienceModal ref="experienceModal" v-if="experienceModalOpened" :open="true"
                @close="experienceModalOpened = false" @experienceUpdated="notifyParent()">
                <template #title>
                    <h3>Add experience</h3>
                </template>


                <template #rightButton>
                    <button @click="this.$refs.experienceModal.addExperience()" class="btn btn-warning save">Save</button>
                </template>
            </ExperienceModal>
        </div>

        <hr>

    </div>
</template>

<style>
.fieldNameContainerExperiences,
.fieldNameExperiences {
    font-weight: 700;

    display: inline-block;
}

.fieldNameContainerExperiences {
    padding: 0rem 0.5rem;
}

.experience {
    display: block;
    padding: 0rem 0.5rem;
    margin-top: 1rem;
    border-radius: 0.3rem;
}

.add-experience-btn-container {
    display: inline-block;
    margin-left: 2rem;

}

.add-experience-btn {
    background-color: orange;
}

.add-experience-btn:hover {
    background-color: rgb(255, 184, 52);
}


.experience:hover {
    cursor: pointer;
    background-color: rgba(128, 128, 128, 0.348);
}

.experience-title {
    font-weight: 600;
    font-size: 120%;
}


.experience-date {
    font-style: italic;
    font-size: 80%;
}

.experience-description {
    margin-top: 0.5rem;
}


.value-container {
    cursor: text;
    min-width: 8rem;
    padding: 0.25rem 0.5rem;
    border-radius: 0.2rem;

}

.value-container:hover {
    cursor: text;
    background-color: rgba(128, 128, 128, 0.348);

}

.value {
    display: inline-block;

}

.colleague-value {


    cursor: text;
    min-width: 8rem;
    padding: 0.25rem 0.5rem;
    border-radius: 0.2rem;


}

.fieldNameContainer,
.fieldName {
    float: left;
    font-weight: 700;
    display: inline-block;

}

.fieldNameContainer {
    padding: 0.25rem 0.5rem;
}

.fieldValue {
    margin-left: 1%;
    display: inline-block;
}

.biography.active {}
</style>