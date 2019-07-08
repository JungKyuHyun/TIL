# Hidden class

자바스크립트는 동적 타이핑(dynamic typing) 언어입니다. 정적 타이핑(static typing) 언어를 사용하면, 가변길이 배열(variable-length array)과 같은 동적인 데이터 타입을 사용하지 않는 이상, 프로퍼티(혹은 구조체의 멤버)의 메모리 오프셋을 컴파일 시에 결정할 수 있습니다. 따라서 프로퍼티를 선언할 때 오프셋 값을 어딘가에 저장해 둔 뒤, 각 프로퍼티의 값이 필요할 때 오프셋 값을 그대로 사용하면 되는 것이지요.

반면에 데이터 타입이 동적으로 정해지는 코드는 프로퍼티를 선언했을 때의 프로퍼티의 데이터 타입이나 순서가 실제로 프로퍼티 값을 접근할 때는 달라질 수 있기 때문에 프로퍼티의 메모리 오프셋을 컴파일할 때 결정하는 것은 불가능합니다. 따라서 프로퍼티 값을 읽어야 할 때 마다 프로퍼티를 찾아내야 합니다. 즉 동적 탐색(dynamic lookup)이 필요합니다. 자바스크립트처럼 사전형(dictionary) 형태의 객체를 이용한다면, 객체의 프로퍼티를 읽어들일 때 비용이 발생하며 이 비용은 구현 방식에 따라 달라집니다.

V8은 히든 클래스를 이용하여 동적 탐색(Dynamic Lookup)을 회피하고 있습니다. 한마디로 말하자면, 프로퍼티가 바뀔 때 각각 그 프로퍼티의 오프셋을 업데이트한 뒤 그 값을 가지고 있는 방식입니다.

히든 클래스에는 다음과 같은 특징이 있습니다.

- 객체는 반드시 하나의 히든 클래스를 참조한다.
- 히든 클래스는 각 프로퍼티에 대해 메모리 오프셋을 가지고 있다.
- 동적으로 새로운 프로퍼티가 만들어질 때, 혹은 기존 프로퍼티가 삭제되거나 기존 프로퍼티의 데이터 타입이 바뀔 때는 신규 히든 클래스가 생성되며, 신규 히든 클래스는 기존 프로퍼티에 대한 정보를 유지하면서 추가적으로 새 프로퍼티의 오프셋을 가지게 된다.
- 히든 클래스는 프로퍼티에 대해 변경이 발생했을 때 참조해야 하는 히든 클래스에 대한 정보를 갖는다.
- 객체에 새로운 프로퍼티가 만들어지면, 현재 참조하고 있는 히든 클래스의 전환 정보를 확인한 후, 현재 프로퍼티에 대한 변경이 전환 정보의 조건과 일치하면, 객체의 참조 히든 클래스를 조건에 명시된 히든 클래스로 변경시킨다.