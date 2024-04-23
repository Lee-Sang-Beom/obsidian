
### watch()

- 지정된 인풋을 관찰(watch)하고, 그 값들을 반환하며, 렌더링 할 대상을 결정할 때 유용합니다.
    
- defaultValue가 정의되지 않은 경우, register가 아직 호출이 안되었기 때문에 watch의 첫번째 렌더링에서는 undefined 을 반환합니다. 하지만, 두번째 인수로 defaultValue를 설정하여 값을 반환 할 수 있습니다.
    
- useForm 에서 defaultValues로 정의가 되어 있다면, 첫번째 렌더링에서 defaultValues에 적용된 내용을 반환합니다.
    

### getValues()

- 폼의 값을 읽을 때 사용합니다. watch 와 다르게 getValues​​ 는 **리랜더링을 일으키거나 입력값의 변화를 구독하지 않는다**는 것입니다. 이 함수는 아래의 방식으로 사용합니다.

- getValues​​(): 폼의 전체 값을 읽습니다.
    
- getValues​​('test'): 폼 안의 개별 인풋 값을 읽습니다.name.
    
- getValues​​(['test', 'test1']): 인풋의 name 속성을 지정하여 여러 값을 읽습니다.