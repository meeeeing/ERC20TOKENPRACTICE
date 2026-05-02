# ERC20 Token Practice

---

## 1. 스마트 컨트랙트란?

스마트 컨트랙트는 블록체인 위에 배포되어 실행되는 프로그램이다.  
일반적인 프로그램과 달리 중앙 서버에서 실행되는 것이 아니라, 블록체인 네트워크에 의해 실행되고 그 결과가 블록체인에 기록이 된다.

스마트 컨트랙트는 한 번 배포되면 특정 주소를 가지고 사용자는 트랜잭션을 통해 컨트랙트의 함수를 호출할 수 있다.  
예를 들어 설명하자면 토큰 발행, 토큰 전송, NFT 발행, 투표, 탈중앙화 거래소, 대출 프로토콜 등의 기능을 스마트 컨트랙트로 구현할 수 있다.

아래 작성한 ERC20 토큰들도 스마트 컨트랙트의 한 예시이다.  
토큰 이름, 심볼, 총 발행량, 잔액 조회, 토큰 전송 등의 기능이 컨트랙트 코드로 정의되어 있고, Sepolia 테스트넷에 배포된 뒤 블록체인 상에서 실행된다.

---

## 2. EOA와 CA의 차이

Ethereum 계정은 크게 EOA와 CA로 나눌 수 있다.

### EOA

EOA는 Externally Owned Account의 약자이다.
개인키를 가진 사용자가 직접 제어하는 계정이다.
MetaMask와 같은 지갑에서 생성되는 주소가 대표적인 EOA이다.

EOA는 직접 트랜잭션을 만들고 서명할 수 있다.  
예를 들면 사용자가 MetaMask로 토큰을 전송하거나 스마트 컨트랙트 배포를 승인하는 경우 해당 작업은 EOA를 통해 이루어진다.

### CA

CA는 Contract Account의 약자이다.  
스마트 컨트랙트가 블록체인에 배포되면서 생성되는 계정이다.

CA는 개인키로 직접 제어되는 것이 아니라 컨트랙트 내부에 작성된 코드에 따라 동작한다.  
즉, 사용자의 트랜잭션이나 다른 컨트랙트의 호출을 받으면 그에 따라 코드가 실행된다.

### 정리

 EOA vs. CA  

의미 : 사용자가 제어하는 외부 계정 vs. 컨트랙트가 배포되며 생성되는 계정
제어 방식: 개인키로 제어 vs. 코드 로직으로 제어
예 :  MetaMask 지갑 주소 vs. ERC20 토큰 컨트랙트 주소 
트랜잭션 생성: 직접 생성 가능 vs. 직접 생성 불가, 호출을 받아 실행됨 

코드에서 MetaMask의 내 지갑 주소는 EOA이고, Sepolia에 배포한 ERC20 토큰 주소는 CA이다.

---

## 3. EIP와 ERC란?

### EIP

EIP는 Ethereum Improvement Proposal의 약자이다.  
Ethereum 네트워크의 기능, 프로토콜, 표준, 개선 사항 등을 제안하고 문서화하기 위한 형식이다.

Ethereum 생태계에서 새로운 기능이나 규칙을 제안할 때 EIP 문서로 정리하고 논의와 검토 과정을 거친다.

### ERC

ERC는 Ethereum Request for Comments의 약자이다.  
EIP 중에서도 애플리케이션 계층에서 사용하는 표준을 다루는 경우가 많다.

대표적인 ERC 표준
- ERC20 : 대체 가능한 토큰 표준 
- ERC721 : NFT와 같은 대체 불가능한 토큰 표준 
- ERC1155 : 여러 종류의 토큰을 하나의 컨트랙트에서 다룰 수 있는 표준 

코드에서 구현한 ERC20은 대체 가능한 토큰을 만들기 위한 표준이다.  
ERC20 표준은 토큰의 총 발행량 조회, 계정별 잔액 조회, 토큰 전송, 사용 승인, 대리 전송 등의 기능을 정의한다.

---

## 4.코드

OpenZeppelin 라이브러리를 사용하지 않고 ERC20 표준의 핵심 기능을 직접 구현한 버전과 OpenZeppelin의 ERC20 컨트랙트를 상속하여 작성한 버전 총 2가지 버전으로 작성했다.
두 코드를 모두 Sepolia 테스트넷에 배포하고, 배포된 컨트랙트 주소를 함께 정리했다.

### 4.1 ERC20 토큰

ERC20 은 A가 B에게 전송하는거라고 간단하게 보면된다 
A의 balance가 -가 되고 B의 balance가 +가 되는 단순한 동작이라고 이해했다.
```text
ManualERC20Token.sol
```

컨트랙트 이름
```text
MiizManualToken
```


```text
totalSupply()
balanceOf(address account)
transfer(address to, uint256 amount)
approve(address spender, uint256 amount)
allowance(address owner, address spender)
transferFrom(address from, address to, uint256 amount)
Transfer 이벤트
Approval 이벤트
```

직접 구현 버전에서는 `mapping`을 사용해서 각 주소의 토큰 잔액과 승인 정보를 저장했다.

```solidity
mapping(address => uint256) private _balances;
mapping(address => mapping(address => uint256)) private _allowances;
```

`_balances`는 각 주소가 보유한 토큰 수량을 저장한다.
`_allowances`는 특정 사용자가 다른 주소에게 얼마만큼의 토큰 사용 권한을 허락했는지 저장한다.

ERC20의 기본 구조인 잔액 조회, 토큰 전송, 승인, 대리 전송 기능을 구현하였다.


---

### 4.2 OpenZeppelin을 사용한 ERC20 토큰
공부하다보니 OpenZeppelin 버전에 대한 글을 보게되었는데 실제 개발에서 검증된 라이브러리를 사용하면 훨씬 간단하고 안전하게 토큰을 작성할 수 있다고 하여 사용해보았다.

```text
OpenZeppelinERC20Token.sol
```

컨트랙트 이름
```text
MiizOZToken
```

이 코드는 OpenZeppelin에서 제공하는 ERC20 컨트랙트를 상속하여 작성한 버전이다.

OpenZeppelin의 ERC20 구현에는 `transfer`, `approve`, `transferFrom`, `balanceOf`, `totalSupply`와 같은 ERC20 표준 기능이 이미 구현되어 있다.
따라서 이 버전에서는 ERC20을 상속하고, 생성자에서 토큰 이름과 심볼을 지정한 뒤 초기 발행량을 배포자에게 발행하였다.

```solidity
import {ERC20} from "@openzeppelin/contracts/token/ERC20/ERC20.sol";

contract MiizOZToken is ERC20 {
    constructor(uint256 initialSupply) ERC20("MiizOZToken", "MOZ") {
        _mint(msg.sender, initialSupply * 10 ** decimals());
    }
}
```



---

## 5. 배포 정보

### 5.1 ERC20 토큰

```text
Token Name: MiizManualToken
Symbol: MMT
Network: Sepolia Testnet
Contract Address: 0x08ff2d720a0e00004b01735b72e2a1944f55b4ff
```

Etherscan에서 확인해보니 컨트랙트 배포 트랜잭션이 성공적으로 처리되었고 배포자 주소로 1,000,000 MMT가 발행되었다.

---

### 5.2 OpenZeppelin ERC20 토큰

```text
Token Name: MiizOZToken
Symbol: MOZ
Network: Sepolia Testnet
Contract Address: 0xa5dc1ddf921c5739126f7c0ba7335f4c771f8cba
```

Etherscan에서 확인해보니 컨트랙트 배포 트랜잭션이 성공적으로 처리되었고 배포자 주소로 1,000,000 MOZ가 발행되었다.

---

## 6. 두 구현 방식 비교

| 구분    | 직접 구현 버전             | OpenZeppelin 버전                    |
| ----- | -------------------- | ---------------------------------- |
| 파일명   | ManualERC20Token.sol | OpenZeppelinERC20Token.sol         |
| 컨트랙트명 | MiizManualToken      | MiizOZToken                        |
| 구현 방식 | ERC20 기능을 직접 작성      | OpenZeppelin ERC20 상속              |
| 코드 길이 | 상대적으로 길다             | 짧다                                 |
| 장점    | ERC20 내부 구조 이해에 좋다   | 검증된 구현을 사용할 수 있다                   |
| 단점    | 실수 가능성이 있다           | 내부 구현을 직접 확인하지 않으면 구조 이해가 부족할 수 있다 |
| 사용 목적 | 학습용                  | 실무에 가까운 방식                         |

ManualERC20Token은 ERC20 표준이 어떤 함수와 이벤트로 구성되어 있는지 이해하는 데 도움이 되었고 OpenZeppelin 버전은 이미 검증된 ERC20 구현을 상속하기 때문에 코드가 짧고 안정적인 방식으로 토큰을 작성할 수 있었다.

---

## 7. 실습 환경

```text
IDE: Remix IDE
Wallet: MetaMask
Network: Sepolia Testnet
Solidity Version: ^0.8.20
Initial Supply: 1,000,000
```

---

## 8. 참고 자료

* Solidity 기초 2: ERC-20 스마트 컨트랙트 배포하기
  [https://medium.com/hy-block/solidity-기초-2-erc-20-스마트-컨트랙트-배포하기-ab2c302557d5](https://medium.com/hy-block/solidity-기초-2-erc-20-스마트-컨트랙트-배포하기-ab2c302557d5)

* WEMIX Docs - ERC-20 Contract
  [https://docs.wemix.com/ko/tutorial/erc-20-token/erc-20-contract](https://docs.wemix.com/ko/tutorial/erc-20-token/erc-20-contract)

* ERC-20 Token Standard
  [https://eips.ethereum.org/EIPS/eip-20](https://eips.ethereum.org/EIPS/eip-20)

* OpenZeppelin Contracts - ERC20
  [https://docs.openzeppelin.com/contracts/5.x/erc20](https://docs.openzeppelin.com/contracts/5.x/erc20)
