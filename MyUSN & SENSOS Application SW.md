# SENSOS 교육/개발 시스템
### MyUSN 소개
- SENSOS(Seloco USN SOC Solution)
  - 셀로코(주)에서 개발한 유무선 센서 네트워크 SOC 및 시스템 솔루션
- MyUSN Station 구성
  - SG100F(SENSOS Gateway System)
  - SN100S(SENSOS Sensor Node)
- 각 보드 모델명의 끝자리 'F'와 'S'는 적용된 기술을 구분하여 FGPA기반 또는 SOC기반을 의미
  - FPGA(Field Programmable Gate Array)
    - 설계 가능 논리 소자와 프로그래밍 가능한 내부 회로가 포함된 반도체 소자
    - 일반적인 프로세서와 달리 프로세서 내부 회로를 프로그램에 맞게 설계해놓고 곧바로 병렬적으로 실행시킴으로써 훨씬 빠른 계산 속도를 낼 수 있음
  - SOC(System On Chip)
    - 하나의 집적회로에 집적된 컴퓨터나 전자 시스템 부품을 가리키는 단일 칩 시스템
    - 주로 모바일 기기, 아두이노와 같은 전력 소비가 적은 저전력 소비 디바이스에 사용
- 범용 8bit MCU와 표준 JPEG encoder가 하나의 칩에 통합된 영상처리 프로세서 기반의 플랫폼
  - MCU(Micro Controller Unit)
    - 집적회로 안에 프로세서와 메모리, 입출력 버스 등 최소한의 컴퓨팅 요소를 내장한 초소형 컨트롤러
- USN 환경에서 발생하는 다양한 정보를 수집하고 제공하기 위한 유무선 센서 네트워크 시스템 개발용 플랫폼

### MyUSN 특징
- USN 교육을 위한 표준 플랫폼 지원
- 센서 네트워크용 Embedded OS SenQ51(ETRI 기술이전 : NanoQplus를 8051 MCU에 포팅)
- 홈 네트워크, 빌딩관리, 환경 모니터링 등 다양한 응용 센서 제공
- 간편한 칩 안테나를 적용한 RF 모듈
- 높은 전송속도, 신뢰성을 보장하는 유선 Ethernet 통신 지원
- 확장성, 개방성을 보장하는 무선 RF 통신 지원
- 이미지센서 기반의 영상과 환경 센서를 융합한 복합 기술 지원

### SG100F
- FPGA기반의 8051 MCU와 JPEG encoder를 중심으로 UART, Ethernet, ZigBee, WiFi 등의 통신 모듈과 온도, 조도, 연기, 기압, 동작, 거리, 터치, 온습도, 이미지 등의 센서 모듈 및 LCD, LED와 Push Switch 등 다양한 입출력 소자들을 포함하고 있다.
- 메모리맵
  - FGPA에는 8bit MCU인 SU8051과 JPEG encoder가 내장
  - 64KB의 프로그램 메모리 및 64KB의 외부 데이터 메모리 내장
    - 프로그램 메모리
      - 사용자가 만든 응용 프로그램이 저장되는 메모리로, Pm39LV010 Flash Memory 적용
    - 외부 데이터 메모리(8000h~FFFFh)
      - 프로그램 수행 중 필요한 데이터를 저장하는 용도의 메모리로, IDT71V124SA SRAM 적용
      - E000h~FFFFh : Ethernet 통신 데이터 수신용 내부 buffer 영역
      - C000h~DFFFh : Ethernet 통신 데이터 송신용 내부 buffer 영역
      - A300h~A3FFh : RTC(Real Time Clock) 모듈 구동을 위한 영역
      - A000h~A2FFh : IO Select 영역
      - 9400h~97FFh : 간접 어드레싱 방식에 의한 JPCON 제어 영역
      - 8000h~8FFFh : Ethernet 제어 관련 영역
- Clock Interface
  - SG100F는 안정적인 동작 클럭 공급을 위해 48MHz OSC를 이용함
  - FPGA 내부에서 클럭을 관리하는 리소스인 DCM 모듈을 통하여 22.1184MHz로 변환시켜 MCU 블록과 JPEG 블록에 제공
    - DCM(Digital Clock Manager)
      - FPGA 내부에서 클럭 신호를 조작하고 회로에 오류가 발생하는 클럭 스큐 방지에 사용
  - SU8051 MCU와 JPEG encoder의 동작 주파수는 22.1184MHZ이며, CMOS 이미지 센서 모듈에는 22.1184MHz의 클럭이 공급됨
- 센서부(총 9종의 센서)
  - FPGA에 직접 연결되어 있는 CMOS 이미지 센서와 온습도 센서를 제외한 나머지 센서는 아날로그 출력값을 갖기 때문에 별도의 ADC IC와 연결
- 통신부
  - UART
    - 스위치 제어를 통해 SU8051이 데이터버퍼(74HC574)를 통해 2개의 스위치 소자(74HC125)를 제어하여 하나의 UART 인터페이스로 Host PC와 FPGA 내부 MCU(SU8051)와 ZigBee 모듈 간에 통신 가능
    - SU8051 프로그래밍을 통한 스위치 제어가 아니라 수동으로 제어할 수 있도록 제공되는 DIP 스위치(SW4)를 조작하면 Host PC와 MCU 간에 UART 통신을 수행하도록 설정 가능
  - ZigBee(LW2455-EM)
    - LM2455-EM은 2.4GHz의 비면허 ISM 주파수 대역에 기반을 둔 ZigBee RF 모듈
    - RF Transceiver, RF 회로, 8051-compatible MCU와 몇몇 외부 소자들로 구성되어 있으며, IEEE802.15.4에 있는 PHY, MAC 규격과 ZigBee 규격을 따름
    - 250Kbps에서 1Mbps까지의 data rate를 지원하며, SG100F에서는 2개의 20핀 커넥터를 통해 ZigBee 모듈과 UART로 통신
    - LM2455-EM은 2개의 UART가 있어서 UART0는 외부 디바이스와의 Serial 통신을 수행하고, UART1은 ZigBee 프로그래밍 동작을 수행 가능
  - Ethernet
    - 인터넷망을 통해 웹서버에 접속하거나 원활한 데이터 송수신을 위한 유선 Ethernet 기능을 지원
    - Hardwired TCP/IP 칩인 WIZNET W5100과 MAC-JACK 등으로 구성
    - FPGA에 내장된 8bit MCU에서는 Ethernet 모듈을 일반 SRAM에 접근하듯이 제어할 수 있고, 이를 통해 Ethernet 모듈을 이용한 웹 기반 네트워크 카메라 기능을 제공
    - 카메라 기능 사용 시에 특별한 영상 전송 프로토콜을 가지지 않으며, 이미지 요청과 전송은 HTTP 프로토콜 사용
    - Ethernet 모듈 특징
      - Supports 10/100 Base TX
      - Supports half/full duplex operation
      - Supports auto-negotiation and auto crossover detection
      - IEEE 802.3/802.3u Complaints
      - Operates 3.3V with 5V I/O signal tolerance
      - Includes Hardware Internet protocols : TCP, IPv4, UDP, ICMP, ARP, PPPoE, IGMP
      - Includes Hardware Ethernet protocols : DLC, MAC
      - Supports 4 independent connections simultaneously
      - Supports MCU bus Interface and SPI Interface
      - Supports Direct/Indirect mode bus access
      - Supports Socket API for easy application programming
  - Wi-Fi(WizFi630)
    - WizFi630은 RS-232 프로토콜과 TCP/IP 프로토콜을 IEEE802.11b/g/n 무선랜 프로토콜로 변환시키는 모듈이다.
    - WizFi630은 RS-232 Serial Interface가 장착된 장비를 LAN 또는 WLAN 망에 연결하여 원격 측정, 관리 및 제어를 가능하게 한다.
    - WizFi630은 Embedded Switch를 내장하고 있어서 IP 공유기로서의 기능도 수행한다.
    - Serial(UART), LAN, Wi-Fi(WLAN)로 이루어진 인터페이스들을 이용하여 Serial-to-Wi-Fi, Serial-to-Ethernet, Ethernet-to-Wi-Fi 등의 기능을 수행할 수 있다.
    - WizFi630은 802.11b/g/n 규격을 따르면서 무선 인터페이스에서 150Mbps의 속도까지 지원
    - Wi-Fi 모듈의 특징
      - Complies with IEEE802.11b/g/n
      - Gateway / AP(Bridge) / AP-Client / client(Station) / Ad-hoc Mode, WDS / Repeater supports
      - 1T1R RF Interface
      - Physical link rate up to 150Mbps
      - Built-in 3 Ethernet Ports
      - 2 Serial Ports supports
      - Working as Wi-Fi Router
      - WEP 64/128bit, WPA/WPA2-PSK TKIP, AES
      - 802.1x (Only in AP mode)
      - 802.11e and WMM (Wi-Fi Multimedia)
      - Router and Firewall function supports
- 입출력부
  - PUSH Switch
    - 외부 입력 신호 제어를 위해 사용
    - Push Switch와 연결된 P3.2와 P3.3은 FPGA에 내장된 MCU의 외부 인터럽트 INT0과 외부 인터럽트 INT1의 역할을 수행함
  - LED / 7-Segment / CLCD / GLCD 제어
    - LED/7-Segment/LCD 제어는 UC5000C의 데이터 버스를 사용하며 이를 제어하기 위하여 외부 데이터 메모리 A000h번지와 A100h번지를 접근하여 /IOSEL1신호와 /IOSEL2신호를 활성화시켜 신호를 전달
    - /IOSEL2는 사용할 디바이스를 선택하는데 사용되며, /IOSEL1은 선택한 소자에 제어신호나 데이터 신호를 전달하는데 사용
  - External GPIO
    - UC5000C의 GPIO인 Port1과 Port3의 신호 중 임의의 신호를 사용자가 설계한 보드나 모듈의 신호에 연결해 실험할 수 있도록 10핀 Box 커넥터 형태로 External GPIO Port 2개를 제공
    - UC5000C의 Port1과 Port3 신호들은 SG100F 보드 내부의 다양한 디바이스에 이미 연결되어 사용되고 있기 때문에 보드 내부의 다른 디바이스의 영향을 받지 않도록 GPIO 신호선을 차단해야 함
    - External GPIO를 사용하지 않는 핀의 경우 우측으로 설정해야지만 보드 내부 디바이스의 기능을 정상적으로 활용 가능
  - FPGA Extension Pin
    - 사용자의 다양한 응용기술 개발을 위하여 10핀 Box 커넥터 형태로 16개의 Extension Pin 제공
    - J13과 J16의 확장 핀 XP1~XP16은 FPGA의 일반 I/O 핀 16개에 1:1로 직접 연결되어 있음
    - FPGA 확장 핀을 활용하기 위해서는 FPGA 내부에 구현된 UC5000C 기능에 하드웨어 레벨에서 원하는 기능을 사용자 모듈로 추가/설계 통합한 후 다시 FPGA에 구현해야 함
    - 제공하는 입출력 장치 외의 입출력 장치를 사용하고자 할 경우 Extension Pin에 리본 케이블 등을 연결하여 사용할 수 있으며, SG100F 보드 두 개를 이용하여 데이터를 주고받으면서 실험 가능
- RTC부
  - RTC 모듈 DS12C887은 별도의 어드레스 입력이 없으며, 내부에 배터리가 내장되어 있어서 외부 전원공급 없이 10년 동안 동작 가능
  - 비휘발성 메모리가 113B 있어서 전원이 없어도 데이터 값이 지워지지 않음

### SN100S
- 셀로코(주)에서 자체 개발한 USN SOC인 UC5000_R2 칩이 메인 프로세서로 장착되어 있음
- 영상 복합센서 네트워크 시스템의 센서노드로 사용하기 적합하도록 SG100F의 다양한 센서와 입출력 방식 중에 선별하여 적용
- 센서부는 온도/습도/조도 센서와 영상 센서노드에 필수인 이미지 센서를 기본으로 장착하였고, 움직임 센서와 거리 센서는 별도 옵션보드로 제공함으로써 사용자 요구에 따라 확장 가능
- 통신부는 ZigBee 모듈을 기본으로 장착하였고 Ethernet 통신 모듈은 옵션보드로 제공되어 필요에 따라 확장할 수 있음
- 사용자 응용프로그램을 SN100S의 Flash Memory에 다운로드 하기 위한 Writer 모듈도 옵션보드로 함께 제공됨
- 메모리맵
  - 64KB의 프로그램 메모리와 64KB의 외부 데이터 메모리 내장
    - 프로그램 메모리
      - 프로그램에 들어가는 메모리로 Pm39LV010 Flash Memory가 사용
    - 외부 데이터 메모리(8000h~FFFFh)
      - 프로그램 수행 중에 사용되는 데이터를 읽고 쓰는 메모리이며, SN100S에서는 SRAM IDT71V124SA 사용
      - E000h~FFFFh : Ethernet 통신 데이터 수신용 내부 buffer 영역
      - C000h~DFFFh : Ethernet 통신 데이터 송신용 내부 buffer 영역
      - 9400h~97FFh : 간접 어드레싱 방식에 의한 JPCON 제어 영역
  - UC5000C에 내장된 JPCON(영상압축 기능 수행)의 parameter 값을 세팅하거나 JPCON 블록의 enable 신호를 활성화하는 등 SU8051로부터 직간접 어드레싱 액세스를 통한 명령어 처리를 수행
- 센서부
  - 이미지센서(YACBAC1SDDAS)
    - ISP가 내장되어 있어서 YUV 형태의 raw 이미지 영상 데이터 Y_0~Y_7을 출력하고 이때 HSYNC, VSYNC, PCLK 등의 제어신호도 함께 출력됨
    - CIS의 동작을 위해서는 초기 power sequence 과정을 거쳐서 사용자가 원하는 영상출력을 위한 내부 레지스터 세팅값을 SU8051을 통해서 입력하게 됨
    - CIS와의 통신은 P1_6 신호와 P1_7 신호를 통한 I2C 방식을 사용
- 통신부
  - UART
    - 1개의 Mini USB port를 통해서 UART 통신을 수행하는데 USB to dual UART Bridges 칩(CP2105)을 사용하여 2개의 Serial Port 설정 가능
    - 하나는 ZigBee 모듈과 Flash Memory를 프로그래밍할 때 사용하고 다른 하나는 FPGA와 ZigBee 모듈간의 Serial 통신을 위해서 사용
    - S/W 제어를 통해 SU8051이 데이터 버퍼소자(74HC574)를 통해 2개의 스위치 소자(74HC125)를 제어하여 하나의 UART 통신으로 외부 PC와 FPGA, ZigBee 간에 통신 가능
  - ZigBee(RP-MR500)
    - 2.4GHz RF Transceiver, Baseband Modem, Hardware MAC, 8051 MCU, Flash Memory를 내장하였으며 칩 안테나가 부착된 IEEE802.15.4 기반의 ZigBee 모듈로써 RF출력은 최대 8dB 정도
    - SG100F에 적용된 LM2455-EM 모듈보다 크기를 대폭 줄인 SMT 형태로 개발되어 구성품의 소형화가 필요한 SN100S에 적용하기 적합하며 I/O 신호들도 L42455-EM과 바로 호환 가능
- 입출력부
  - PUSH Switch
    - 외부 입력 제어를 위한 1개의 PUSH 스위치를 지원
    - UC5000C 칩 내부 8bit MCU의 인터럽트0 신호로 연결
  - LED
    - 다양한 종류의 데이터를 디스플레이하기 위해 제공
    - LED를 켜기 위해서는 해당 포트핀이 'low' 되어야 함
- 옵션 보드
  - SO100W(Sensor Writer 모듈)
    - CP2102, Bus Transceiver Chip, Buffer Chip 등으로 구성
    - SN100S에 장착된 Flash Memory를 프로그래밍하는 Sensor Node 모듈
    - SO100W의 특징
      - SN100S 프로그래밍 가능
      - PC 연결시 USB-to-UART Bridge 포트로 인식
      - ISP 전용 AVR 탑재
      - 동작 확인용 LED
    - 다운로드 모듈을 탈부착할 수 있게 하여 개발완료 후 보드 크기 및 저전력 측면에서 유리하도록 설계
  - SO100R(움직임 센서)
    - 인체감지 및 움직임감지를 위해 동작센서, Fresnel Lens를 적용한 동작센서 모듈
    - 동작 센서 D203B와 아날로그 입력신호를 증폭해주는 2단 증폭기 등으로 구성
  - SO100D(거리 센서)
    - 거리감지를 위해 IR 신호 기반의 거리센서가 장착된 옵션 모듈
    - 거리 센서 GP2Y0A21와 전원 소음 방지회로 등으로 구성
  - WIZ810MJ(TCP/IP 모듈)
    - WIZNET W5100칩과 MAC-JACK 등으로 구성된 TCP/IP 통신용 네트워크 모듈
    - TCP/IP Hardwired Chip과 PHY를 내장하는 W5100을 포함하고 있기 때문에 빠르게 인터넷을 사용할 수 있는 시스템을 개발하기를 원하는 사용자들에게 적합
    - WIZ810MJ의 특징
      - Support 10/100 Base TX
      - Suppoert half/full duplex operation
      - Supports auto-negotiation and auto crossover detection
      - IEEE 802.3/802.3u complaints
      - Operates 3.3V with 5V I/O signal tolerance
      - Supports network status indicator LEDs
      - Includes Hardware Internet protocols : TCP, IP Ver.4, UDP, ICMP, ARP, PPPoE, IGMP
      - Includes Hardware Ethernet protocols : DLC, MAC
      - Supports 4 independent connections simultaneously
      - Supports MCU bus Interface and SPI Interface
      - Supports Direct/Indirect mode bus access
      - Supports Socket API for easy application programming
      - Interfaces with Two 2.0mm pitch 2 * 14 header pin
      - Temperature : 0 ~ 70°C (Operation), -40 ~ 85°C (Storage)

### SENSOS Application SW
- SEMS
  - 네트워크를 활용하여 영상 또는 센서 데이터를 수집, 저장, 관리 및 필요한 처리를 하는 시스템
  - 셀로코(주)는 SEMS에서 사용하는 응용 프로그램으로 SEMS Server, SEMSView-PC, SEMSView-Mob을 제공
- SEMS Server
  - 수집된 데이터를 저장하는 역할과 동시에 사용자가 조회 가능하도록 서비스를 제공
- SEMSView-PC & SEMSView-Mob
  - 저장된 데이터를 모니터링하는 응용 프로그램
  - SEMSView-PC
    - 사무실이나 관제 센터에서 PC를 사용하여 모니터링 가능
  - SEMSView-Mob
    - 스마트 기기에 설치하여 인터넷 접속만 가능하면 장소에 제한없이 모니터링 가능