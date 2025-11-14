<script setup lang="ts">
import type { FormSubmitEvent } from '@nuxt/ui';
import * as z from "zod";

const props = defineProps<{
    open: boolean;
}>();

const emit = defineEmits(['close', 'created']);

const localOpen = ref<boolean>(props.open)

const loading = ref<boolean>(false);

const toast = useToast();

const schema = z.object({
    code: z.string().length(2),
    name: z.string().min(3).max(255),
    description: z.string().min(5).optional(),
})

type Schema = z.infer<typeof schema>

const personType = reactive<Schema>({
    code: '',
    name: '',
    description: '',
})


watch(() => props.open, (newVal) => {
    localOpen.value = newVal;
});

const onSubmit = async (event: FormSubmitEvent<Schema>) => {
    loading.value = true;
    try {
        await useFetchRequest('/api/catalogs/person-type', {
            method: 'POST',
            body: event.data
        });

        toast.add({
            title: $t('common.success'),
            description: $t('catalogs.personType.messages.createSuccess'),
            icon: 'i-heroicons-check-circle',
            color: 'success',
        });

        emit('created');

        resetForm();

    } catch (error) {
        toast.add({
            title: $t('common.error'),
            description: $t('catalogs.personType.errors.createFailed'),
            icon: 'i-heroicons-x-circle',
            color: 'error',
        });
    } finally {
        loading.value = false;
    }
}

const close = () => {
    emit('close');
}

const resetForm = () => {
    personType.code = '';
    personType.name = '';
    personType.description = '';
}


</script>

<template>

    <UModal v-model:open="localOpen" size="lg" @close:prevent="close" @update:open="(val) => { if (!val) close() }">
        <template #header>
            <h2 class="text-lg font-medium text-gray-900 dark:text-white">
                {{ $t('catalogs.personType.form.create.title') }}
            </h2>
        </template>
        <template #body>
            <UForm
                id="create-person-type-form"
                :schema="schema"
                :state="personType"
                @submit="onSubmit"
                class="space-y-6"
            >
            
                <UFormField :label="$t('catalogs.personType.form.code') + ' *'" name="code" class="space-y-2 w-full">
                    <UInput
                        v-model="personType.code"
                        :label="$t('catalogs.personType.form.code') + ' *'"
                        :placeholder="$t('catalogs.personType.form.code_placeholder')"
                        class="w-full"
                    />
                </UFormField>

                <UFormField :label="$t('catalogs.personType.form.name') + ' *'" name="name" class="space-y-2 w-full">
                    <UInput
                        v-model="personType.name"
                        :label="$t('catalogs.personType.form.name') + ' *'"
                        :placeholder="$t('catalogs.personType.form.name_placeholder')"
                        class="w-full"
                    />

                </UFormField>

                <UFormField :label="$t('catalogs.personType.form.description')" name="description" class="space-y-2 w-full">
                    <UTextarea
                        v-model="personType.description"
                        :label="$t('catalogs.personType.form.description')"
                        :placeholder="$t('catalogs.personType.form.description_placeholder')"
                        class="w-full"
                        :rows="4"
                    />
                </UFormField>
            </UForm>
        </template>
        <template #footer >
            <div class="flex justify-end space-x-2 w-full">
                <UButton type="button" color="error" @click="close">
                    {{ $t('common.cancel') }}
                </UButton>
                <UButton type="submit" color="primary" form="create-person-type-form" :loading="loading">
                    {{ $t('common.save') }}
                </UButton>
            </div>
        </template>

    </UModal>

</template>