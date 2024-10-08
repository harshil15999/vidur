<script setup lang="ts">
import type { Hook } from "~/server/db/schema";

import { createHookSchema, updateHookSchema } from '~/schemas/hook';

const { saveHook, updateHook, isSubmitting } = await useHooks();

const props = defineProps<{
  hook?: Hook;
}>();

const emits = defineEmits<{
  created: [];
  updated: [];
}>();

const isUpdating = !!props.hook;

const formSchema = toTypedSchema(isUpdating ? updateHookSchema : createHookSchema);

const { handleSubmit, errors, defineField, handleReset } = useForm({
  validationSchema: formSchema,
});

// @ts-expect-error
const [id] = defineField("id");

const [title] = defineField("title");
const [url] = defineField("url");

if (isUpdating) {
  id.value = props.hook.id;
  title.value = props.hook.title;
  url.value = props.hook.url;
}

const onSubmit = handleSubmit(async values => {
  let success;

  if (isUpdating) {
    success = await updateHook(values);
  } else {
    success = await saveHook(values);
  }

  if (!success) return;

  if (isUpdating) {
    emits("updated");
  } else {
    emits("created");
  }
})

const clear = () => {
  if (isUpdating) {
    throw new Error("Can not call clear when updating");
  }
  title.value = undefined;
  url.value = undefined;
  handleReset();
}

defineExpose({
  clear,
});
</script>

<template>
  <form class="flex flex-col items-start" @submit="onSubmit">
    <div class="flex flex-col justify-items-start w-4/5">
      <label class="text-sm flex" for="title">Title <span class="text-rose-500">*</span></label>
      <div class="flex">
        <input id="title" name="title" class="input-custom" placeholder="My External ATS Provider" type="text"
          v-model="title" :disabled="isSubmitting" />
        <div class="text-xs mt-1 text-rose-500">{{ errors.title }}</div>
      </div>
    </div>
    <div class="flex flex-col justify-items-start w-4/5 my-4">
      <label class="flex text-sm" for="url">URL <span class="text-rose-500">*</span></label>
      <div class="flex">
        <input id="url" name="url" class="input-custom" placeholder="https://ats-provider.com/api/register-event"
          type="url" v-model="url" :disabled="isSubmitting" />
        <div class="text-xs mt-1 text-rose-500">{{ errors.url }}</div>
      </div>
    </div>
    <InputButton :disabled="isSubmitting" type="submit">
      {{ isUpdating ? "Save" : "Create" }}
    </InputButton>
  </form>
</template>
