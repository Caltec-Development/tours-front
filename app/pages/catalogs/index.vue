<template>
  <div class="bg-gray-50">
    <main class="max-w-8xl mx-auto">
      <div class="px-4 py-6 sm:px-0">

        <!-- Catalogs Grid -->
        <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
          <div
            v-for="catalog in catalogs"
            :key="catalog.title"
            class="bg-white rounded-lg shadow-md hover:shadow-lg transition-all duration-300 overflow-hidden cursor-pointer transform hover:-translate-y-1"
            @click="navigateToCatalog(catalog)"
          >
            <!-- Card Header -->
            <div :class="[catalog.color, 'px-6 py-4']">
              <div class="flex items-center justify-between">
                <div class="flex items-center space-x-3">
                  <div class="w-10 h-10 bg-white bg-opacity-20 rounded-lg flex items-center justify-center">
                    <i :class="[catalog.icon, 'text-black text-lg']"></i>
                  </div>
                  <h3 class="text-xl font-semibold text-white">{{ catalog.title }}</h3>
                </div>
                <span
                  v-if="catalog.count > 0"
                  class="bg-white bg-opacity-20 text-black px-3 py-1 rounded-full text-sm font-medium"
                >
                  {{ catalog.count }} elementos
                </span>
              </div>
            </div>

            <!-- Card Body -->
            <div class="p-6">
              <p class="text-gray-600 mb-4 text-sm leading-relaxed">{{ catalog.description }}</p>

              <div class="flex items-center justify-between">
                <button
                  v-if="catalog.available && catalog.can"
                  class="inline-flex items-center px-4 py-2 bg-gray-900 text-white text-sm font-medium rounded-lg hover:bg-gray-800 transition-colors duration-200 disabled:bg-gray-300 disabled:cursor-not-allowed cursor-pointer"
                  @click.stop="navigateToCatalog(catalog)"
                >
                  <i class="fas fa-arrow-right mr-2"></i>
                  Gestionar
                </button>
                <span
                  v-else
                  class="inline-flex items-center px-4 py-2 bg-gray-300 text-gray-500 text-sm font-medium rounded-lg cursor-not-allowed"
                >
                  <i class="fas fa-clock mr-2"></i>
                  Próximamente
                </span>

                <div class="flex items-center text-sm text-gray-500">
                  <i
                    :class="[
                      'mr-1',
                      catalog.count > 0 ? 'fas fa-check-circle text-green-500' : 'fas fa-exclamation-circle text-orange-500'
                    ]"
                  ></i>
                  {{ catalog.count > 0 ? 'Configurado' : 'Sin configurar' }}
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
export default {
  name: 'CatalogsList',
  props: {
    
  },
  data() {
    return {
        catalogs: [
          {
            title: 'Catálogo de Tipos de persona',
            description: 'Gestiona y personaliza tu catálogo de tipos de persona.',
            icon: 'i-heroicons-user-group',
            color: 'bg-blue-600',
            count: 12,
            route: 'catalogs.person-types',
            available: true,
            can: true
          },
        ]
    }
  },
  methods: {
    navigateToCatalog(catalog) {
      if (!catalog.available || catalog.route === '#') {
        return;
      }

      // Navigate to the catalog route
      window.location.href = catalog.route.startsWith('http') ? catalog.route : `/${catalog.route.replace('catalogs.', 'catalogs/')}`;
    },

  }
}
</script>

<style scoped>
/* Additional component-specific styles if needed */
.grid > div {
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}
</style>
