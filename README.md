스레드를 생성한다고 했을 때, 생성된 스레드(이후 '자식 스레드'라 부르겠음)가 접근할 수 있는 부모 스레드(?)의 변수 영역에 대해서 이해가 가지 않음.

자식 스레드를 생성할 때, 매개변수로 부모 스레드의 변수를 넘겨주지 않으면 자식 스레드가 사용할 수 있는 부모 스레드의 변수는 없다고 생각하고 있었음.
(ex: 메인 스레드에서 bank 객체를 생성했다.
스레드 1, 스레드2를 생성할때 매개변수로 전달해주지 않으면 스레드 1, 2가 메인 스레드의 bank 객체에 접근이 불가능 하다.)

**질문 1)** 
스레드를 생성하는 순간, 자식 스레드는 부모 스레드의 정보(인스턴스 변수 등)을 복사 하거나 접근이 가능한가?
자식 스레드의 스택 영역이 부여되는 순간, 해당 공간에는 아무것도 들어있지 않은가 아니면 부모 스레드의 스택 영역을 복사하거나 접근할수 있는 무언가의 방법이 들어가게 되는가?


이후의 서술은 스레드를 생성하는 순간 자식 스레드의 스택은 비어있는 상태라 가정하고 진행하겠음
(스레드 생성 당시 파라미터 등으로 부모 스레드의 객체 등의 접근 방법을 지원하지 않으면 접근 불가)


해당 TCP-CHAT 프로젝트는 'ChatServer'를 열어 다수의 'ChatClient'들이 상호소통이 가능하게 하는 프로젝트 이다.
스레드 풀을 100으로 제한하고, 입장한 'ChatServer'를 HashMap으로 관리한다.

처음 서버를 시작할 때, 스레드를 하나 생성해 새로 연결을 시도하는 'ChatServer'가 있는지 확인한 후, 소켓을 열어주도록 한다.
소켓을 열어준 뒤, 생성된 'SocketClient'객체를 스레드 풀의 스레드에서 관리하게 한다.

(SocketClient.java파일의 21번 줄에서 SocketClient() 생성자가 호출된다)
**질문 2)** 
SocketClient.java 파일의 생성자(15~28)과 receive()(30~56)을 보면
31번 줄에서 새로운 스레드를 생성한 뒤, 스레드 풀에 넘겨서 스레드 처리를 하는것을 확인할 수 있다.

24번 줄에서  receive()를 호출하기 전에는 기존 스레드에서 'SocketClient'객체를 생성하다가, receive()가 호출되면서 백지 상태(스레드 스택에 아무것도 들어가 있지 않은)의 새로운 스레드가 생성되는것으로 이해했다.

**이때, 새로운 스레드는 기존 스레드에서 생성한 'SocketClient'객체에 대한 정보가 없는 상태인거 아닌가??**
하지만 34번 줄의 dis.readUTF()의 'dis'에 접근하는 것을 보면 기존 스레드에서 생성한 SocketClient'객체에 대한 정보를 모두 들고 있는 것으로 생각된다.

이게 람다식으로 스레드 생성을 표시해서 이러한 상황이 발생하는 것인지, 
main함수가 아닌 별개의 class에서 스레드를 생성하는 과정에서 이러한 상황이 발생하는 것인지,
아니면 내가 처음부터 이해를 잘못한 것인지 모르겠다.

