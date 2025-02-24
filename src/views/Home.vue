<script>
import { isDark, useDarkModeHandler } from "@/composable/useDarkModeHandler";
import { useHead } from "@vueuse/head";
import { Icon } from "@iconify/vue";
import { ref } from "@vue/reactivity";
import { inject } from "@vue/runtime-core";
export default {
  components: { Icon },
  setup() {
    const file = ref(null);
    let fileList = ref([]);

    function onChange() {
      fileList.value = [...file.files, ...fileList.value];
    }
    function remove(i) {
      fileList.value.splice(i, 1);
    }
    function dragover(event) {
      event.preventDefault();
      // Add some visual fluff to show the user can drop its files
      if (!event.currentTarget.classList.contains("bg-green-300")) {
        event.currentTarget.classList.remove("bg-gray-100");
        event.currentTarget.classList.add("bg-green-300");
      }
    }
    function dragleave(event) {
      // Clean up
      event.currentTarget.classList.add("bg-gray-100");
      event.currentTarget.classList.remove("bg-green-300");
    }
    function drop(event) {
      event.preventDefault();
      file.files = event.dataTransfer.files;
      console.log(event.dataTransfer.files);
      onChange(); // Trigger the onChange event manually
      // Clean up
      event.currentTarget.classList.add("bg-gray-100");
      event.currentTarget.classList.remove("bg-green-300");
    }
    const logoutHandler = inject("logout");

    useHead({
      title: "Segmentation",
    });
    return {
      fileList,
      useDarkModeHandler,
      isDark,
      file,
      logoutHandler,
      onChange,
      remove,
      dragover,
      dragleave,
      drop,
    };
  },
};
</script>

<style lang="scss" scoped>

</style>



<template> 
  <div class="w-full h-full  "
  >
    <header class="w-full h-24 dark:bg-blue-700 bg-gradient-to-r from-indigo-400 to-purple-300 px-4">
      <div
        class="
          w-full
          h-full
          text-gray-200
          flex
          items-center
          justify-between
        "
      >
        <img src="@/assets/brain.png" width="100" />

        <div class="grid grid-cols-4 gap-8">
          <RouterLink
            tag="p"
            to="/"
            class="text-gray-700 dark:text-gray-200 text-lg font-semibold"
          >
            Brain Segmentation
          </RouterLink>
          <RouterLink
            tag="p"
            to="/file-list"
            class="text-gray-700 dark:text-gray-200 text-lg font-medium"
          >
            File List
          </RouterLink>
        </div>

        <label class="dark-mode ml-auto ">
          <input
            type="checkbox"
            :checked="!isDark"
            @change="useDarkModeHandler"
          />
          <span></span>
        </label>

        <Icon
          @click="logoutHandler"
          icon="feather:log-out"
          width="30"
          class="text-gray-100"
          style="background-color: transparent; margin-left: 20px"
        />
      </div>
    </header>
    <div class="w-full sm:h-96 py-10">
      <img
        src="@/assets/brain.png"
        class="bg-transparent float-left p-5"
        width="100"
      />

      <p class="text-md md:text-2xl w-full text-gray-800 font-sans font-semibold">
        Segment App is an online MRI brain volumetry system. It is intended to
        help researchers all over the world to obtain automatically volumetric
        brain information from their MRI data without the need for any
        infrastructure in their local sites. volBrain works in a fully automatic
        manner and is able to provide brain structure volumes without any human
        interaction. We encourage you to use the system hoping you find it
        useful. The number of cases each user can submit daily is limited to 10
        cases in order to share our limited computational resources between all
        users. This evaluation version of volBrain is free for non-commercial
        and non-medical purposes. Please contact demo@demo.dk or
        pierrick.coupe@labri.fr for processing large amount of data. We are
        looking for collaboration to evaluate and improve our platform, please
        contact us with any feedback.
      </p>
    </div>

    <div
      class="
        p-12
        bg-transparent
       text-gray-200
        border border-gray-200
        md:m-20
        h-96
        flex
        rounded-lg
        items-center
        justify-center
      "
      @dragover="dragover"
      @dragleave="dragleave"
      @drop="drop"
    >
      <input
        type="file"
        multiple
        name="fields[assetsFieldHandle][]"
        id="assetsFieldHandle"
        class="w-px h-px opacity-0 overflow-hidden absolute"
        @change="onChange"
        :ref="file"
        accept=".pdf,.jpg,.jpeg,.png"
      />

      <label for="assetsFieldHandle" class="block cursor-pointer">
        <div class="text-gray-100 text-xl font-sans">
          Explain to our users they can drop files in here or
          <span class="underline">click here</span> to upload their files
        </div>
      </label>
      <ul
        class="mt-4 flex flex-col items-center justify-center"
        v-if="fileList.length"
        v-cloak
      >
        <li class="text-sm p-1" v-for="file in fileList" :key="file">
          {{ file.name
          }}<button
            class="ml-2"
            type="button"
            @click="remove(fileList.indexOf(file))"
            title="Remove file"
          >
            remove
          </button>
        </li>
      </ul>
    </div>
  </div>
</template>

