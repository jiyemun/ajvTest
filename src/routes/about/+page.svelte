<script>
    import {onMount} from 'svelte';
    import {Input, Form, FormGroup, Row, Col} from '@sveltestrap/sveltestrap';

    let action = {}

    let targetType = ''
    let detailType = ''
    let testArgs = [
        {name: 'x', type: 'string'},
        {name: 'y', type: 'int'},
        {name: 't', type: 'float'},
        {name: 'z', type: 'boolean'},
        {name: 'e', type: ''},
    ]

    onMount(async () => {
        await testApi();
    })

    async function testApi() {
        try {
            const response = await fetch('https://jsonplaceholder.typicode.com/todos/1');
            if (response.ok) {
                console.log(response.json())
                action = {
                    name: '',
                    remarkt: '',
                    targetType: [
                        {
                            id: 5,
                            name: 'Robot Type',
                            detailType: [
                                {
                                    id: 1,
                                    name: 'Waypoint',
                                    argsTypes: [
                                        {
                                            id: 9,
                                            actionTypeId: 4,
                                            dataType: 'int',
                                            defaultValue: '0.0',
                                            dataUnit: 'sec',
                                            maxValue: 3600,
                                            minValue: 0,
                                            remark: 'time(second) setting',
                                            isEditable: 1,
                                            seq: 1,
                                            name: 'time(second) setting',
                                        },
                                    ],
                                    paramsTypes: [
                                        {
                                            id: 67,
                                            actionTypeId: 4,
                                            displaySeq: 1,
                                            param: 'id',
                                            dataType: 'string',
                                            defaultValue: '',
                                            remark: 'A unique id that can wake up this standby',
                                            isEditable: 1,
                                            useYn: 1,
                                            name: 'wake up ID',
                                        },
                                    ],
                                },
                                {
                                    id: 2,
                                    name: 'Teaching point',
                                    detailType: [{}],
                                },
                            ],
                        },
                        {
                            id: 6,
                            name: 'Robot Device Type',
                            detailType: [],
                        },
                    ],
                };
            }
        } catch (e) {
            console.log(e)
        }
    }

    let selectedTargetType = null;
    let selectedDetailType  = null;


    // targetType 변경 핸들러
    function handleTargetTypeChange(event) {
        const selectedId = parseInt(event.target.value);
        selectedTargetType = action.targetType.find((type) => type.id === selectedId);
        selectedDetailType = null; // targetType이 변경되면 detailType 초기화
    }

    function getInputType(type) {
        switch (type) {
            case 'int':
                return 'number'
                break;
            case 'float':
                return 'number'
                break;
            case 'boolean':
                return 'switch'
                break;
            default:
                return 'text'
        }
    }
</script>

<Form>
    <FormGroup>
        <Input type="text" placeholder={'name'.toUpperCase()}></Input>
    </FormGroup>
    <FormGroup>
        <Input type="text" placeholder={'remark'.toUpperCase()}></Input>
    </FormGroup>
    <FormGroup>
        {#if action.targetType && action.targetType.length > 0}
            <Input bind:value={targetType} type="select" on:change={handleTargetTypeChange}>
                <option value="">Select a target type</option>
                {#each action.targetType as item, index}
                    <option value={item.id}>{item.name}</option>
                {/each}
            </Input>
        {/if}
    </FormGroup>
    <FormGroup>
        {#if selectedTargetType && selectedTargetType.detailType.length > 0}
            <Input type="select">
                <option value="">Select a detail type</option>
                {#each selectedTargetType.detailType as detail}
                    <option value={detail.id}>{detail.name}</option>
                {/each}
            </Input>
        {/if}
    </FormGroup>
    <Row>
        {#each testArgs as arg}
            <Col sm="3" md="6">
                <FormGroup>
                    <Input type={getInputType(arg.type)} label={arg.name} placeholder={arg.name}>
                        {arg.name}
                    </Input>
                </FormGroup>
            </Col>
        {/each}
    </Row>
</Form>

<style>

</style>