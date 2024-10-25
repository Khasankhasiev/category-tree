<template>
    <div class="p-6 bg-gray-100 rounded-lg shadow-md max-w-lg mx-auto">
        <label class="flex items-center mb-4">
            <input
                type="checkbox"
                v-model="showEmpty"
                @change="fetchRubrics"
                class="mr-2 h-4 w-4 text-blue-600 rounded focus:ring-blue-500"
            />
            <span class="text-gray-800">Отображать пустые рубрики</span>
        </label>

        <p class="text-lg font-semibold text-gray-700 mb-4">
            Общая сумма: <span class="text-blue-600">{{ selectedCount }}</span>
        </p>

        <ul class="space-y-2">
            <TreeNode
                v-for="category in categories"
                :key="category.id"
                :category="category"
                :showEmpty="showEmpty"
                @toggle="toggleSelection"
            />
        </ul>
    </div>
</template>

<script>
import TreeNode from './TreeNode.vue';

export default {
    components: { TreeNode },
    data() {
        return {
            categories: [],
            showEmpty: true,
            selectedCategories: new Set(),
        };
    },
    computed: {
        selectedCount() {
            let total = 0;
            this.selectedCategories.forEach(({ count }) => {
                total += count;
            });
            return total;
        },
    },
    methods: {
        async fetchRubrics() {
            const allowEmptyParam = this.showEmpty ? 1 : 0;
            const response = await fetch(
                `https://www.klerk.ru/yindex.php/v3/event/rubrics?allowEmpty=${allowEmptyParam}`
            );
            if (response.ok) {
                this.categories = await response.json();
            } else {
                console.error('Ошибка загрузки данных:', response.status);
            }
        },
        toggleSelection(id, count, selected) {
            if (selected) {
                this.selectedCategories.add({ id, count });
            } else {
                this.selectedCategories.forEach(category => {
                    if (category.id === id) {
                        this.selectedCategories.delete(category);
                    }
                });
            }
        },
    },
    mounted() {
        this.fetchRubrics();
    },
};
</script>
