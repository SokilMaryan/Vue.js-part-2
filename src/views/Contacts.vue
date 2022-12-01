<template>
  <div class="flex justify-between items-center gap-4">
    <div class="flex gap-3">
      <h3 class="font-medium m-0">Contact list</h3>
      <AppButton @click="createNewContact">
        <template #icon>
          <IconPlus class="w-5 h-5" />
        </template>
        Add Contact
      </AppButton>

      <!-- search contacts input-->
      <div class="flex justify-center items-center gap-2">
        <AppInput
          v-model="inputSearchQuery"
          class="w-[350px]"
          placeholder="Search"
          type="text"
        />
        <!-- error search massage-->
        <div v-if="inputSearchQuery&&!filteredListContact().length" class="item error">
          <p>No results found!</p>
        </div>
      </div>
    </div>

    <!-- role filter contacts-->
    <div class="flex items-center justify-center gap-2">
      <select
        v-model="selected"
        class="rounded-md font-medium border border-gray-medium focus:border-gray-dark text-sm p-2 "
        @change="filteredListContact"
      >
        <option value disabled selected>Please select a role</option>

        <option
          v-for="contact in contacts"
          :key="contact.id"
        >
          {{ contact.role }}
        </option>
      </select>

      <!-- default, ascending, descending sorting contacts-->
      <select
        v-model="selectedSort"
        class="rounded-md font-medium border border-gray-medium focus:border-gray-dark text-sm p-2 "
        @change="filteredListContact"
      >
        <option value disabled selected>Please select a sort</option>
        <option
          v-for="sort in sortingWay"
          :key="sort"
        >
          {{ sort }}
        </option>
      </select>
    </div>
  </div>

  <div class="grid-cols-[repeat(auto-fill,_minmax(320px,_1fr))] grid gap-5 my-5">
    <ContactItem
      v-for="contact in filteredListContact()"
      :key="contact.id"
      class="cursor-pointer"
      :contact="contact"
      @click="editContact(contact.id)"
      @delete="deleteContact"
      @save="updateContact"
    />
  </div>
</template>
<script lang="ts" setup>
import { useRouter } from 'vue-router'
import { storeToRefs } from 'pinia'
import { useContactsStore } from '@/store'
import { ref } from 'vue'

import ContactItem from '@/components/ContactItem.vue'
import AppButton from '@/components/AppButton.vue'
import IconPlus from '@/components/icons/IconPlus.vue'
import AppInput from '@/components/AppInput.vue'

const router = useRouter()
const inputSearchQuery = ref('')
const selected = ref('')
const selectedSort = ref('')
const sortingWay = ['default', 'ascending', 'descending']

const contactsStore = useContactsStore()
const { contacts } = storeToRefs(contactsStore)
const { updateContact, deleteContact } = contactsStore

function createNewContact () {
  router.push({ name: 'upsertContact', params: { contactId: 'new' } })
}

function editContact (contactId: number) {
  router.push({ name: 'upsertContact', params: { contactId } })
}

// function sortedArray () {
//   contacts.value.filter((contact) =>
//     contact.name.includes(selectedSort.value)
//   ).sort((a, b) => {

//     if(selectedSort.value === 'ascending') {
//       return a < b
//     }
//   }

//   // if (selectedSort.value === 'ascending') {
//   //   return names.sort((a, b) => a < b)
//   // } else if (selectedSort.value === 'descending') {
//   //   return names.sort((a, b) => a > b)
//   // } else names
// }

function filteredListContact () {
  if (inputSearchQuery.value) {
    return contacts.value.filter((contact) =>
      contact.name.toLowerCase().includes(inputSearchQuery.value.toLowerCase()) ||
      contact.description.toLowerCase().includes(inputSearchQuery.value.toLowerCase())
    )
  } else if (selected.value) {
    return contacts.value.filter((contact) =>
      contact.role.includes(selected.value)
    )
  }
  return contacts.value
}

</script>
