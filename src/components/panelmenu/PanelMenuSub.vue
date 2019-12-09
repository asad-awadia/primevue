<template>
    <ul class="p-submenu-list">
        <template v-for="(item, i) of model">
            <li role="menuitem" :class="getItemClass(item)" :style="item.style" v-if="item.visible !== false && !item.separator" :key="item.label + i">
                <router-link v-if="item.to" :to="item.to" class="p-menuitem-link" @click.native="onItemClick($event, item)">
                    <span :class="['p-menuitem-icon', item.icon]"></span>
                    <span class="p-menuitem-text">{{item.label}}</span>
                </router-link>
                <a v-else :href="item.url||'#'" class="p-menuitem-link" :target="item.target" @click="onItemClick($event, item)">
                    <span :class="getSubmenuIcon(item)" v-if="item.items"></span>
                    <span :class="['p-menuitem-icon', item.icon]"></span>
                    <span class="p-menuitem-text">{{item.label}}</span>
                </a>
                <transition name="p-toggleable-content">
                    <div class="p-toggleable-content" v-show="item === activeItem">
                        <sub-panelmenu :model="item.items" v-if="item.visible !== false && item.items" :key="item.label + '_sub_'" />
                    </div>
                </transition>
            </li>
            <li class="p-menu-separator" :style="item.style" v-if="item.visible !== false && item.separator" :key="'separator' + i"></li>
        </template>
    </ul>
</template>

<script>
export default {
    name: 'sub-panelmenu',
    props: {
		model: {
            type: null,
            default: null
        }
    },
    data() {
        return {
            activeItem: null
        }
    },
    methods: {
        onItemClick($event, item) {
            if (item.disabled) {
                event.preventDefault();
                return;
            }

            if (!item.url && !item.to) {
                event.preventDefault();
            }

            if (item.command) {
                item.command({
                    originalEvent: event,
                    item: item
                });
            }

            if (this.activeItem && this.activeItem === item)
                this.activeItem = null;
            else
                this.activeItem = item;
        },
        getItemClass(item) {
            return ['p-menuitem', item.className, {'p-disabled': item.disabled}];
        },
        getSubmenuIcon(item) {
            const active = (item === this.activeItem);
            return ['p-panelmenu-icon pi pi-fw', {'pi-caret-right': !active, 'pi-caret-down': active}];
        }
    }
}
</script>