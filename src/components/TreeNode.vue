<template>
    <li>
        <div
            class="flex items-center justify-between bg-white p-3 rounded-lg shadow-sm hover:bg-gray-50"
        >
            <input
                type="checkbox"
                :checked="isSelected"
                @change="handleSelect"
                class="mr-2 h-4 w-4 text-blue-600 rounded focus:ring-blue-500"
            />
            <a
                target="_blank"
                :href="`https://www.klerk.ru${category.url}`"
                class="flex-1 text-gray-900 font-medium hover:text-blue-600"
            >
                {{ category.title }}
                <span class="text-gray-500"
                    >({{ category.count }} / {{ totalSubCount }})</span
                >
            </a>
            <button
                @click="toggleOpen"
                class="ml-2 text-blue-500 hover:text-blue-700"
            >
                {{ isOpen ? '-' : '+' }}
            </button>
        </div>
        <ul
            v-if="isOpen && category.children && category.children.length > 0"
            class="pl-4 mt-2 space-y-2"
        >
            <TreeNode
                v-for="child in category.children"
                :key="child.id"
                :category="child"
                :showEmpty="showEmpty"
                @toggle="toggleSelection"
            />
        </ul>
    </li>
</template>

<script>
export default {
    props: {
        category: Object,
        showEmpty: Boolean,
    },
    data() {
        return {
            isOpen: false,
            isSelected: false,
        };
    },
    computed: {
        totalSubCount() {
            return this.category.count + this.getChildrenCount(this.category);
        },
    },
    methods: {
        toggleOpen() {
            this.isOpen = !this.isOpen;
        },
        handleSelect(event) {
            this.isSelected = event.target.checked;
            this.$emit(
                'toggle',
                this.category.id,
                this.totalSubCount,
                this.isSelected
            );
        },
        getChildrenCount(category) {
            if (!category.children) return 0;
            return category.children.reduce(
                (sum, child) =>
                    sum + child.count + this.getChildrenCount(child),
                0
            );
        },
    },
};
</script>
