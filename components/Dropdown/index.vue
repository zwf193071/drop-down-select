<!--
 * @Author: 朱文芳
 * @Description: 下拉控件
 * @LastEditors: 朱文芳
 * @UpdateLogs: 下拉控件
 * @Date: 2019-10-09 11:09:57
 * @LastEditTime: 2019-10-09 16:17:06
 -->

<template>
    <div
        class="ice-dropdown"
        @click="handleClick"
        v-click-outside="onClickoutside"
    >
        <div :class="prefixCls">
            <div v-show="dropDown.chosenItems.length">
                <Tag
                    v-for="(item, i) of dropDown.chosenItems"
                    :key="`${item}-${i}`"
                    closable
                    @on-close="v => { handleClose(i) }"
                >{{item}}</Tag>
            </div>
            <i class="ivu-icon ivu-icon-ios-arrow-down ice-select-arrow"></i>
        </div>
        <transition name="transition-drop">
            <ul
                class="ice-select-dropdown"
                :placement="placement"
                v-show="currentVisible"
                v-transfer-dom
                @click="(e)=>{e.stopPropagation();}"
            >   
                <CheckboxGroup v-model="dropDown.chosenItems" @on-change="checkChosenItems">
                    <li
                        v-for="(item, i) of data"
                        :key="`${item}-${i}`"
                    >{{item}}<Checkbox :label="item"></Checkbox></li>
                </CheckboxGroup>
            </ul>
        </transition>
    </div>
</template>
<script>
/**
 *  component: 下拉组件
 *  author: zhuwenfang
 *  lastDate: 2019/10/16
 *  使用：
 *      props:
 *           1. placement   下拉控件显示位置，默认为bottom，该功能待完善
 *           2. data    下拉控件显示的数据信息
 *           3. dropDown   选中的值
 *     emitEvents:
 *           1. chosenItems 选中单条的事件
 *           2. deleteItems 删除单条的事件
 * 
 **/
import {directive as clickOutside} from 'v-click-outside-x';
import TransferDom from '@/directives/transfer-dom';
import { oneOf } from "@/utils/assist";

const prefixCls = ['ice-select-con'];
export default {
    name: 'Dropdown',
    directives: { clickOutside, TransferDom },
    props: {
        placement: {
            validator (value) {
                return oneOf(value, ['top', 'top-start', 'top-end', 'bottom', 'bottom-start', 'bottom-end', 'left', 'left-start', 'left-end', 'right', 'right-start', 'right-end']);
            },
            default: 'bottom'
        },
        data: {
            type: Array,
            default: () => {
                return ['underlay', 'overlay', 'fireWall', 'loadBalance']
            }
        },
        dropDown: {
            type: Object,
            default: () => {
                return {
                    chosenItems: []
                }
            }
        }
    },
    data() {
        return {
            prefixCls: prefixCls,
            currentVisible: false
        }
    },
    computed: {
        transition () {
            return ['bottom-start', 'bottom', 'bottom-end'].indexOf(this.placement) > -1 ? 'slide-up' : 'fade';
        }
    },
    methods: {
        onClickoutside () {
            this.currentVisible = false;
            this.prefixCls = [...prefixCls]
        },
        handleClick () {
            this.currentVisible = !this.currentVisible;
            this.prefixCls = [...prefixCls, {
                ['ice-select-visible']: this.currentVisible
            }]
        },
        checkChosenItems () {
            this.$emit('chosenItems', this.dropDown.chosenItems)
        },
        handleClose (i) {
            this.dropDown.chosenItems.splice(i, 1)
            this.$emit('deleteItems', this.dropDown.chosenItems)
        }
    },
}
</script>
<style scoped>
.ice-dropdown {
    position: relative;
    cursor: pointer;
}
.ice-dropdown .ice-select-con {
    position: relative;
    height: 32px;
    line-height: 28px;
    padding-left: 3px;
    text-align: left;
    box-sizing: border-box;
    border: 1px solid #E2E8F0;
    border-radius: 4px;
}
.ice-dropdown .ice-select-visible.ice-select-con {
    border-color: #57a3f3;
    outline: 0;
    box-shadow: 0 0 0 2px rgba(45,140,240,.2)
}
.ice-select-visible .ice-select-arrow {
    -webkit-transform: translateY(-50%) rotate(180deg);
    transform: translateY(-50%) rotate(180deg);
    display: inline-block;
}
.ice-select-arrow {
    position: absolute;
    top: 50%;
    right: 8px;
    line-height: 1;
    -webkit-transform: translateY(-50%);
    transform: translateY(-50%);
    font-size: 14px;
    color: #808695;
    transition: all .2s ease-in-out
}
.ice-select-dropdown {
    padding: 20px 20px 10px;
    min-width: 200px;
    position: absolute;
    will-change: top, left;
    transform-origin: center top;
    top: 45px;
    left: 0;
    background-image: linear-gradient(-134deg, #F9FBFF 0%, #FFFFFF 100%);
    box-shadow: 0 2px 10px 0 rgba(37,93,169,0.25);
    border-radius: 4px;
    z-index: 900;
}
.ice-select-dropdown li {
    float: left;
    margin-right: 10px;
    margin-bottom: 10px;
    padding-left: 8px;
    padding-right: 5px;
    min-width: 80px;
    line-height: 24px;
    font-size: 12px;
    background: #FBFDFF;
    border: 1px solid #E2E8F0;
    border-radius: 4px;
}
.ice-select-dropdown .ivu-checkbox-wrapper {
    float: right;
    margin-left: 10px;
    margin-right: 0;
}
</style>
