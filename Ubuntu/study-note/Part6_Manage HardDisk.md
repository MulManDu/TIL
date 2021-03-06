# 6장. 하드디스크 관리와 사용자별 공간할당
하드디스크를 추가하는 방법을 살펴보고, 하드디스크 여러 개를 하나처럼 운영하는 Raid와 LVM을 알아본다. 또, 우분투를 안전한 RAId에 설치하는 실습도 진행하며, 마지막으로 각 사용자별로
공간 사용량을 제한하는 쿼터도 학습한다.

## 학습흐름
- [x] 하나의 디스크를 추가하는 방식
- [x] RAID의 개념과 정의
- [x] RAID 구현 실습
- [x] RAID 문제 발생과 복구 실습
- [x] LVM의 이해와 실습
- [x] 우분투를 RAID에 설치
- [x] 사용자별 공간 할당 실습

## 배경지식
### SCSI
- Small Computer System Interface , 발음: 스커지 또는 스카시
- 소형 컴퓨터를 위한 주변기기 연결에 쓰이는 인터페이스(Interface) s표준
- 주로 별도의 컨트롤러를 장착한 후, SCSI 장치를 컨트롤러 카드 포트에 꽂아서 사용.
- SCSI 컨트롤러는 CPU와 독립적으로 처리하여 속도가 빠름.
- CPU 점유율이 낮아서 안정성을 요구하는 서버에서 주로 사용됐음.

### SATA
- 직렬 ATA(Serial ATA)
- 하드 디스크 혹은 광학 드라이브와 데이터 전송을 주요 목적으로 만든 컴퓨터 버스의 한 가지이다.

### 파티션 명칭
- SCSI 하드디스크 명칭: sda, sdb, sdc, ...
- SCSI 하드디스크 파티션 명칭: sda1, sda2, sda3, ...

### SWAP 파티션
- 컴퓨터 메모리가 흘러 넘칠 때 댐의 역할을 합니다. 메모리가 완전히 가득 차면 추가적으로 실행되는 프로그램은 메모리가 아닌 스왑 파티션에서 실행됩니다.
- [출처: 리눅스의 스왑 파티션이란 무엇?](http://sergeswin.com/1034)

### RAID
- 하드웨어 RAID는 하드웨어 제조업체에서 여러 개의 하드디스크를 연결할 장비를 만들어서 그 자체를 공급하는 것이다. 하드웨어 RAID는 좀 더 안정적이고, 각 제조업체에서 기술지원을 받을 수 있기에 많이 선호하는 방법이다. 안정적이고 성능 좋은 제품은 상당한 고가다. 대개 하드웨어 RAID는 고가의 경우 SA-SCSI 하드디스크를, 중저가는 SATA 하드디스크를 사용해 만든다.
- 소프트웨어 RAID는 하드디스크만 여러개 있으면 운영체제에서 지원해주는 방식이다. 하드웨어 방식보다 신뢰성과 속도가 떨어지지만 저렴한 비용으로 안전하게 데이터를 저장할 수 있는 장점이 있다. 개념은 같다.
- 복수 배열 독립 디스크(Redundant Array of Independent Disks 혹은 Redundant Array of Inexpensive Disks)는 여러 개의 하드 디스크에 일부 중복된 데이터를 나눠서 저장하는 기술이다. 디스크 어레이(disk array)라고도 한다. 데이터를 나누는 다양한 방법이 존재하며, 이 방법들을 레벨이라 하는데, 레벨에 따라 저장장치의 신뢰성을 높이거나 전체적인 성능을 향상시키는 등의 다양한 목적을 만족시킬 수 있다. [...더보기](https://ko.wikipedia.org/wiki/RAID)


## 실습1
하드디스크 1개를 장착해서 사용해보자
1. VMware 머신 세팅
  - SCSI , 1GB, Virtual disk as a single fie 옵션 선택 후 생성.
2. `$ fdisk` 터미널 실행. 파티션을 sdb1 생성.
3. `$ msfs.ext4 /dev/sdb1` 명령어를 통해서 파일 시스템 형식(ext4)으로 지정했습니다.
4. /mydata 디렉토리를 생성 한 후, 마운트 `$ mount /dev/sdb1 /mydata`
