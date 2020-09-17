<template>
    <table class="audit-resource-options">
        <colgroup>
            <col width="4%">
            <col width="28%">
            <col width="6%">
            <col width="28%">
            <col width="6%">
            <col width="28%">
        </colgroup>
        <tr>
            <td align="right"><label class="option-label">{{$t('操作对象')}}</label></td>
            <td>
                <audit-target-selector class="option-value"
                    searchable
                    category="resource"
                    :placeholder="$t('请选择xx', { name: $t('操作对象') })"
                    v-model="condition.resource_type">
                </audit-target-selector>
            </td>
            <td align="right"><label class="option-label">{{$t('动作')}}</label></td>
            <td>
                <audit-action-selector class="option-value"
                    :target="condition.resource_type"
                    :placeholder="$t('请选择xx', { name: $t('动作') })"
                    :empty-text="$t('请先选择操作对象')"
                    v-model="condition.action">
                </audit-action-selector>
            </td>
            <td align="right"><label class="option-label">{{$t('时间')}}</label></td>
            <td>
                <cmdb-form-date-range class="option-value"
                    font-size="medium"
                    :placeholder="$t('请选择xx', { name: $t('时间') })"
                    v-model="condition.operation_time">
                </cmdb-form-date-range>
            </td>
        </tr>
        <tr>
            <td align="right"><label class="option-label">{{$t('账号')}}</label></td>
            <td>
                <cmdb-form-objuser class="option-value"
                    v-model="condition.user"
                    :exclude="false"
                    :multiple="false"
                    :placeholder="$t('请输入xx', { name: $t('账号') })">
                </cmdb-form-objuser>
            </td>
            <td align="right"><label class="option-label">{{$t('实例')}}</label></td>
            <td>
                <bk-input class="option-value"
                    v-model="instanceFilter"
                    :placeholder="$t('请输入xx', { name: instanceType === 'resource_id' ? 'ID' : $t('名称') })">
                    <bk-select class="option-type" slot="prepend"
                        :clearable="false"
                        v-model="instanceType">
                        <bk-option id="resource_name" :name="$t('名称')"></bk-option>
                        <bk-option id="resource_id" name="ID"></bk-option>
                    </bk-select>
                </bk-input>
            </td>
            <td></td>
            <td>
                <div class="options-button">
                    <bk-button class="mr10" theme="primary" @click="handleSearch">{{$t('查询')}}</bk-button>
                    <bk-button theme="default" @click="handleReset">{{$t('清空')}}</bk-button>
                </div>
            </td>
        </tr>
    </table>
</template>

<script>
    import AuditTargetSelector from './audit-target-selector'
    import AuditActionSelector from './audit-action-selector'
    import RouterQuery from '@/router/query'
    export default {
        name: 'audit-resource-options',
        components: {
            AuditTargetSelector,
            AuditActionSelector
        },
        data () {
            return {
                instanceType: 'resource_name',
                condition: {
                    bk_biz_id: '',
                    resource_type: '',
                    action: [],
                    operation_time: [],
                    user: '',
                    resource_id: '',
                    resource_name: '',
                    category: 'resource'
                }
            }
        },
        computed: {
            instanceFilter: {
                get () {
                    return this.condition[this.instanceType]
                },
                set (value) {
                    this.condition[this.instanceType] = value
                }
            }
        },
        watch: {
            instanceType () {
                this.condition.resource_id = ''
                this.condition.resource_name = ''
            }
        },
        created () {
            this.handleSearch()
        },
        methods: {
            handleSearch () {
                this.$emit('condition-change', this.condition)
                RouterQuery.set({
                    tab: 'resource',
                    page: 1,
                    _t: Date.now()
                })
            },
            handleReset () {
                this.condition = {
                    bk_biz_id: '',
                    resource_type: '',
                    action: [],
                    operation_time: [],
                    user: '',
                    resource_id: '',
                    resource_name: '',
                    category: 'resource'
                }
                this.handleSearch()
            }
        }
    }
</script>

<style lang="scss" scoped>
    .audit-resource-options {
        width: 100%;
        padding: 5px 0;
        tr {
            td {
                padding: 5px 0;
            }
        }
        .option-label {
            font-size: 14px;
            padding: 0 10px;
            @include ellipsis;
        }
        .option-value {
            width: 100%;
            min-width: 230px;
            max-width: 400px;
        }
        .option-type {
            width: 80px;
            margin-top: -1px;
            border-color: #c4c6cc transparent;
            box-shadow: none;
        }
        .options-button {
            display: flex;
            align-items: center;
        }
    }
</style>