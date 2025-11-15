<script setup lang="ts">
import type { TableColumn } from '@nuxt/ui';
import type { PersonType } from '~/interfaces/person-type';

const openCreateModal = ref<boolean>(false);
const openUpdateModal = ref<boolean>(false);
const selectedPersonType = ref<PersonType | null>(null);

const columns: TableColumn<PersonType>[] = [
    {
        accessorKey: 'id',
        header: $t('catalogs.personType.table.columns.id'),
    },
    {
        accessorKey: 'code',
        header: $t('catalogs.personType.table.columns.code'),
    },
    {
        accessorKey: 'name',
        header: $t('catalogs.personType.table.columns.name'),
    },
    {
        accessorKey: 'status',
        header: $t('catalogs.personType.table.columns.status'),
    },
    {
        accessorKey: 'actions',
        header: $t('products.table.columns.actions'),
    }

];

const tableRef = ref<InstanceType<typeof import('~/components/ServerSideTable.vue').default> | null>(null);

const onRefresh = () => {
    openCreateModal.value = false;
    openUpdateModal.value = false;
    selectedPersonType.value = null;
    tableRef.value?.refresh();
}

const editPersonType= (personType: Partial<{
    id: number;
    code: string;
    name: string;
    description: string;
    status: boolean;
}>) => {
    openUpdateModal.value = true;
    selectedPersonType.value = personType as PersonType;
}

</script>

<template>
    <div class="space-y-6">
        <div class="flex items-center justify-between">
            <div>
                <h1 class="text-2xl font-bold text-gray-900 dark:text-white">
                    {{ $t('catalogs.personType.title') }}
                </h1>
            </div>
            <UButton color="primary" size="lg" @click="openCreateModal = true">
                <template #leading>
                    <UIcon name="i-heroicons-plus" />
                </template>
                {{ $t('catalogs.personType.addNew') }}
            </UButton>
        </div>
        <ServerSideTable
            ref="tableRef"
            :columns="columns"
            :pageSizeOptions="[50, 100, 200]"
            :initialPageSize="100"
            url="/api/catalogs/person-type"
        >
            <template #status-cell="{ row }">
                <span v-if="row.original.status" class="text-green-600 font-medium">{{ $t('common.active') }}</span>
                <span v-else class="text-red-600 font-medium">{{ $t('common.inactive') }}</span>
            </template>

            <template #actions-cell="{ row }">
                <div class="flex items-center justify-center space-x-2">
                    <UButton size="md" color="info" variant="outline" @click="editPersonType(row.original)">
                        <UIcon name="i-heroicons-pencil-square" class="mr-1" />
                        {{ $t('catalogs.personType.table.columns.edit') }}
                    </UButton>
                    <CatalogsPersonTypeDelete :item="row.original" @onConfirm="onRefresh"/>
                </div>
            </template>

        </ServerSideTable>
       <CatalogsPersonTypeCreate :open="openCreateModal" @close="openCreateModal = false" @created="onRefresh"/>
       <CatalogsPersonTypeUpdate :open="openUpdateModal" :person-type="selectedPersonType" @close="openUpdateModal = false" @updated="onRefresh"/>
    </div>
</template>