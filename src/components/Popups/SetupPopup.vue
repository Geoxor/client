<template>
  <div class="setup_popup_container">
    <form class="flex flex-col gap-[1rem]">
      <div class="flex flex-col gap-[0.5rem]">
        <h2
          class="text-red-500 indent-2px text-14px"
          v-if="settingsFormError.backendUrlError"
          :style="`${
            settingsFormError.backendUrlError ? '' : 'display: none;'
          }`"
        >
          {{ settingsFormError.backendUrlError }}
        </h2>
        <div :class="`${settingsFormError.backendUrlError ? 'error' : ''}`">
          <input
            class="popupInputField"
            type="text"
            v-model="settingsForm.backend_url"
            placeholder="Backend   URL / Hostname and port e.g. http://127.0.0.1:2234"
            @click="settingsFormError.backendUrlError = ''"
          />
        </div>
      </div>
      <div class="flex flex-col gap-[0.5rem]">
        <h2
          class="text-red-500 indent-2px text-14px"
          v-if="settingsFormError.databaseUrlError"
          :style="`${
            settingsFormError.databaseUrlError ? '' : 'display: none;'
          }`"
        >
          {{ settingsFormError.databaseUrlError }}
        </h2>
        <div :class="`${settingsFormError.databaseUrlError ? 'error' : ''}`">
          <input
            class="popupInputField"
            type="text"
            v-model="settingsForm.database_url"
            placeholder="Database URL / Hostname and port e.g. mongodb://localhost:27017/moefetch:"
            @click="settingsFormError.databaseUrlError = ''"
          />
        </div>
      </div>

      <div
        class="checkbox_option_container pl-[6px] pr-[6px] pt-[8px] pb-[8px] h-[32px] flex flex-row items-center"
      >
        <h1>Prefered quality to download</h1>
        <div class="h-[24px] right-0 flex flex-row items-center">
          <div class="checkbox_option" @click="togglePreferedQuality(true)">
            <Icon
              :icon="
                !settingsForm.prefered_quality_highest_bool
                  ? 'unchecked_checkbox'
                  : 'checked_checkbox'
              "
            />
            <h2>Highest</h2>
          </div>
          <div class="checkbox_option" @click="togglePreferedQuality(false)">
            <Icon
              :icon="
                settingsForm.prefered_quality_highest_bool
                  ? 'unchecked_checkbox'
                  : 'checked_checkbox'
              "
            />
            <h2>Default</h2>
          </div>
        </div>
      </div>

      <div
        class="checkbox_option_container pl-[6px] pr-[6px] pt-[8px] pb-[8px] h-[32px] flex flex-row items-center"
      >
        <h1>Search different sites for possibly higher quality</h1>
        <div class="h-[24px] right-0 flex flex-row items-center">
          <div class="checkbox_option" @click="toggleSearchForDiffSites(true)">
            <Icon
              :icon="
                !settingsForm.search_diff_sites
                  ? 'unchecked_checkbox'
                  : 'checked_checkbox'
              "
            />
            <h2>Yes</h2>
          </div>
          <div class="checkbox_option" @click="toggleSearchForDiffSites(false)">
            <Icon
              :icon="
                settingsForm.search_diff_sites
                  ? 'unchecked_checkbox'
                  : 'checked_checkbox'
              "
            />
            <h2>No</h2>
          </div>
        </div>
      </div>

      <div class="flex flex-col gap-[0.5rem]">
        <h2
          class="text-red-500 indent-2px text-14px"
          v-if="settingsFormError.saucenaoApiKeyError"
          :style="`${
            settingsFormError.saucenaoApiKeyError ? '' : 'display: none;'
          }`"
        >
          {{ settingsFormError.saucenaoApiKeyError }}
        </h2>
        <div :class="`${settingsFormError.saucenaoApiKeyError ? 'error' : ''}`">
          <input
            :class="`${
              settingsForm.search_diff_sites
                ? 'popupSaucenaoKeyInputField'
                : 'popupSaucenaoKeyInputFieldDisabled'
            }`"
            type="text"
            v-model="settingsForm.saucenao_api_key"
            placeholder="Saucenao API key (for the option above)"
            :disabled="!settingsForm.search_diff_sites"
            @click="settingsFormError.saucenaoApiKeyError = ''"
          />
        </div>
      </div>

      <Button
        type="button"
        text="Confirm"
        color="#4d6d8d"
        class="absolute bottom-8 right-8 w-[fit-content] rounded-[8px]"
        @click="submit()"
      />
    </form>
  </div>
</template>

<script setup lang="ts">
import Icon from "../Misc/Icon.vue";

import { onMounted, ref, computed } from "vue";
import Button from "../Misc/Button.vue";
import api from "../../services/api";
import type { ISettings } from "../../services/types";

const settingsFormError = ref<{
  backendUrlError: undefined | string;
  databaseUrlError: undefined | string;
  saucenaoApiKeyError: undefined | string;
}>({
  backendUrlError: "",
  databaseUrlError: "",
  saucenaoApiKeyError: "",
});

const settingsForm = ref<ISettings>({
  backend_url: "",
  database_url: "",
  prefered_quality_highest_bool: false,
  search_diff_sites: false,
  saucenao_api_key: undefined,
});

const localStorageSettings = localStorage.getItem("settings"); //to see if exists

if (localStorageSettings) {
  //const localStorageSettingsJSON:ISettings =  \\/
  settingsForm.value = JSON.parse(localStorageSettings); //localStorageSettingsJSON;
}

function togglePreferedQuality(toggleInto: boolean) {
  settingsForm.value.prefered_quality_highest_bool = toggleInto;
}

function toggleSearchForDiffSites(toggleInto: boolean) {
  settingsForm.value.search_diff_sites = toggleInto;
}

function submit() {
  if (!settingsForm.value.backend_url)
    settingsFormError.value.backendUrlError = "No Backend url was provided";
  if (!settingsForm.value.database_url)
    settingsFormError.value.databaseUrlError = "No Database url was provided";
  if (
    !settingsForm.value.saucenao_api_key &&
    settingsForm.value.search_diff_sites
  )
    settingsFormError.value.saucenaoApiKeyError =
      "No saucenao api key was provided";

  if (
    settingsFormError.value.backendUrlError ||
    settingsFormError.value.databaseUrlError ||
    settingsFormError.value.saucenaoApiKeyError
  )
    return;
  else {
  }
}
</script>

<style lang="postcss">
:root {
  --setup_popup_height: 500px;
  --setup_popup_width: 680px;
}

.setup_popup_container {
  @apply absolute top-[50%] left-[50%] p-10 text-white-400;
  @apply border-[3px] border-[#254EE0] gap-[14px];
  @apply flex-row gap-[4px] m-auto;

  z-index: 1;

  display: flex;
  gap: 32px;

  box-shadow: 0px 2px 4px rgba(255, 255, 255, 0.12);
  border-radius: 4px;
  background-color: rgba(42, 45, 52, 1);

  width: var(--setup_popup_width);
  height: var(--setup_popup_height);

  left: calc((100vw - var(--setup_popup_width)) / 2);
  right: calc((100vw - var(--setup_popup_width)) / 2);
  top: calc((100vh - var(--setup_popup_height) + 7vh) / 2);
}
.popupInputField[type="text"] {
  @apply outline-none h-[2rem] w-[37.5rem] box-border transition duration-100 ease rounded-4px font-medium text-12px border-none px-6px py-2 placeholder-warmGray-400;
  background-color: rgba(28, 27, 34, var(--tw-bg-opacity));
  font-family: "Work Sans", sans-serif;
  color: rgb(202, 202, 202);
}

.popupSaucenaoKeyInputField[type="text"] {
  @apply outline-none h-[2rem] w-[37.5rem] box-border transition duration-100 ease rounded-4px font-medium text-12px border-none px-6px py-2 placeholder-warmGray-400;
  background-color: rgba(28, 27, 34, var(--tw-bg-opacity));
  font-family: "Work Sans", sans-serif;
  color: rgb(202, 202, 202);
}
.popupSaucenaoKeyInputFieldDisabled[type="text"] {
  @apply outline-none h-[2rem] w-[37.5rem] box-border transition duration-100 ease rounded-4px font-medium text-12px border-none px-6px py-2 placeholder-warmGray-500;
  background-color: rgba(28, 27, 34, var(--tw-bg-opacity));
  font-family: "Work Sans", sans-serif;
  color: rgb(129, 129, 129);
}
.checkbox_option_container h1 {
  @apply text-12px w-[100%];
  font-family: "Work Sans", sans-serif;
  color: white;
}
.checkbox_option {
  @apply text-12px flex flex-row items-center h-[24px] align-middle w-[108px] gap-1 cursor-pointer;
  font-family: "Work Sans", sans-serif;
  color: white;
}
.checkbox_option img {
  @apply h-[24px] w-[24px];
}

.error {
  @apply border-rose-600 border-width-[2px] !important;
}
</style>
