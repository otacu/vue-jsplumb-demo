<template>
    <el-container class="flowChartWrap">
        <el-aside width="240px"
                  class="left">
            <el-tree :data="nodeData"
                     node-key="id"
                     :default-expanded-keys="['source','preHandle','sign','learn']"
                     icon-class="el-icon-arrow-right"
                     :render-content="renderContentFunction"
                     :filter-node-method="filterNode"
                     ref="tree2"
                     :props="defaultProps"></el-tree>
        </el-aside>

        <el-main class="main">
            <div class="mainContainer"
                 @drop="dropHandle"
                 @dragover="dragoverHandle">
                <div id="mainContainer"
                     @click="clickBgHandle"></div>
            </div>
        </el-main>

        <el-aside width="300px"
                  class="right" id="nodeInfo">
            <div v-show="!isShowNode">
                <div class="model-attr">
                    <p>
                        <span class="item">id</span>
                        <span class="value">12345678</span>
                    </p>
                    <p>
                        <span class="item">流程名称</span>
                        <span class="value">我的流程</span>
                    </p>
                    <p>
                        <span class="item">备注</span>
                        <el-input type="textarea"
                                  size="small"></el-input>
                    </p>
                </div>
            </div>
            <div v-show="isShowNode">
                <div  class="node-attr">
                    <p>
                        <span class="item">节点ID</span>
                        <span class="value">{{currentNodeId}}</span>
                    </p>
                </div>
            </div>
        </el-aside>
    </el-container>
</template>

<script lang="ts">
    import Vue, { CreateElement } from 'vue';
    import FlowChart from '../FlowChart/index';

    export default Vue.extend({
        data() {
            return{
                isShowNode: false,
                currentNodeId: '',
                isUndoDisable: true,
                isExecDisable: false,
                dialogTableVisible: false,
                nodeData: [],
                defaultProps: {
                    children: 'children',
                    label: 'label',
                },
            }
        },
        mounted () {
            FlowChart.setContainer('mainContainer');
            FlowChart.on('commandListEmpty', () => {
                this.isUndoDisable = true;
            });
            FlowChart.on('showNodeData', () => {
                this.dialogTableVisible = true;
            });
            FlowChart.on('addCommand', () => {
                this.isUndoDisable = false;
            });
            FlowChart.on('selectNode', (data:string) => {
                this.isShowNode = true;
                this.currentNodeId = data;
            });
            // API.getFlowChartData().then((data: any) => {
            //     FlowChart.loadData(data.data);
            // });
            // API.getMenuData().then((data:any) => {
            //     this.nodeData = data.data;
            // });
        },
        methods: {
            renderContentFunction(h: CreateElement, { node, data }:{node:any, data:any}) {
                const className = node.expanded ? 'el-icon-folder-opened' : 'el-icon-folder';
                const classNameChild = (!data.children && data.icon) ? data.icon : '';
                return h('div', {
                    class: { leafNode: !data.children },
                    style: {
                        height: '38px',
                        lineHeight: '38px',
                        fontSize: '12px',
                        color: '#1b1c23',
                    },
                }, [
                    h('el-tooltip', {
                        attrs: {
                            content: data.label,
                            placement: 'top-end',
                            disabled: !!data.children,
                        },
                    }, [
                        h('span', {
                            attrs: {
                                draggable: !data.children,
                                id: data.id,
                            },
                            on: {
                                dragstart: this.dragHandle,
                            },
                            class: 'node',
                            style: {
                                display: 'inline-block',
                                marginTop: '4px',
                                height: '30px',
                                lineHeight: '30px',
                                width: '140px',
                                borderRadius: '4px',
                                position: 'relative',
                                outline: 'none',
                                border: !data.children ? '1px solid transparent' : 'none',
                            },
                        }, [
                            h('i', {
                                class: {
                                    [className]: !!data.children,
                                    [classNameChild]: !data.children,
                                },
                                style: {
                                    color: '#00cdea',
                                    marginLeft: data.children ? '10px' : '15px',
                                },
                            }),
                            h('span', {
                                style: {
                                    fontSize: '13px',
                                    marginLeft: '10px',
                                },
                            }, data.label),
                        ]),
                    ]),
                ]);
            },
            filterNode(value:string, data:any) {
                if (!value) return true;
                return data.label.indexOf(value) !== -1;
            },
            dragoverHandle(ev:Event) {
                ev.preventDefault();
            },
            dragHandle(ev:any) {
                ev.dataTransfer.setData('target', ev.target.id);
            },
            dropHandle(ev:any) {
                FlowChart.addNode({ pageX: ev.pageX, pageY: ev.pageY }, ev.dataTransfer.getData('target'));
            },
            clickBgHandle() {
                this.isShowNode = false;
            },
            zoomOut() {
                FlowChart.zoomOut();
            },
            zoomIn() {
                FlowChart.zoomIn();
            },
            undo() {
                FlowChart.undo();
            },
            execModel() {
                this.isExecDisable = true;
                FlowChart.execModel().then(() => {
                    this.isExecDisable = false;
                });
            },
        },
    })
</script>

<style lang="scss">
    .flowChartWrap {
        height: 100%;
        .left {
            border-right: 1px solid #e5e5e5;
            height: 100%;
            .el-tree {
                height: calc(100% - 40px);
                overflow-y: auto;
            }
        }
        .right {
            border-left: 1px solid #e5e5e5;
        }
        .main {
            .mainContainer {
                height: calc(100% - 42px);
                position: relative;
                overflow: hidden;
                outline: none !important;
                #mainContainer {
                    outline: none !important;
                    height: 100%;
                    width: 100%;
                    position: relative;
                }
            }
        }
        #nodeInfo {
            .model-attr {
                padding: 10px;
                .item {
                    font-size: 12px;
                }
                .value {
                    font-size: 12px;
                    color: #999;
                    margin-left: 10px;
                }
                .el-input {
                    margin-top: 5px;
                }
                textarea {
                    margin-top: 5px;
                    font-family: inherit;
                }
            }
            .node-attr {
                padding: 10px;
                .item {
                    font-size: 12px;
                }
                .value {
                    font-size: 12px;
                    color: #999;
                    margin-left: 10px;
                }
            }
        }
        .tabsNav {
            padding: 0;
            .el-tabs--card > .el-tabs__header .el-tabs__nav {
                border-top: 3px solid #01c1de;
                border-radius: 0;
            }
            .el-tabs__item.is-active {
                color: #333 !important;
            }
            .el-tabs__item {
                font-size: 12px;
            }
            .el-tabs__item:focus.is-active.is-focus:not(:active) {
                box-shadow: none !important;
            }
        }
        .el-tree-node__content,
        .el-tree-node {
            min-height: 38px !important;
        }
        .leafNode {
            .node::before {
                content: "";
                position: absolute;
                top: 2px;
                left: 3px;
                border-radius: 2px;
                padding: 13px 2px;
                background: transparent;
            }
            &:hover span.node {
                border: 1px solid #1c9bec !important;
                background: #fff;
                &::before {
                    background: #1c9bec;
                }
            }
        }
    }
</style>
