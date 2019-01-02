# Blockchain System

### [Introduction about Blockchain System](#introduction-about-blockchain-system-1)
### [Introduction the Blockchain System Platform](#introduction-about-blockchain-system-platform-1)
### [The Applied in Blockchain](#the-applied-in-blockchain-1)
### [Build own Bitcoin Platform](#build-own-bitcoin-platform-1)
### [Blockchain Company & capacity of competition](#blockchain-company--capacity-of-competition-1)
![](https://hyperledger.org/wp-content/uploads/2018/07/Hyperledger_Greenhouse-59-2.png)

## Introduction about Blockchain System
Blockchain là một công nghệ cho phép truyền dữ liệu an toàn với các chính sách và hệ thống mã hóa vô cùng phức tạp, tương tự cuốn sổ cái kế toán của một công ty, nơi mà tiền mặt và mọi hoạt động của công ty được giám sát chặt chẽ. Trong trường hợp này Blockchain là một cuốn sổ cái kế toán hoạt động trong lĩnh vực kỹ thuật số. Hay nói một cách khác Blockchain là một cơ sở dữ liệu trong một mạng và được chia sẻ cho những người tham gia vào mạng lưới.

Blockchain sở hữu tính năng là việc truyền tải dữ liệu (bất cứ cái gì trên đời cũng có thể là dữ liệu) không đòi hỏi một trung gian để xác nhận thông tin. Hệ thống Blockchain tồn tại rất nhiều nút độc lập có khả năng xác thực thông tin mà không đòi hỏi **dấu hiệu của niềm tin**. **Thông tin trong Blockchain không thể bị thay đổi và chỉ được bổ sung thêm khi có sự đồng thuận của tất cả các nút trong hệ thống**. **Người tham gia vào mạng có trách nhiệm phê duyệt các giao dịch và giám sát mạng bằng cách giải quyết các công thức tinh vi với sự trợ giúp của máy tính**. Nó là một hệ thống ngang hàng P2P, loại bỏ tất cả mọi khâu trung gian, làm tăng cường an ninh, minh bạch và sự ổn định cũng như giảm thiểu chi phí và lỗi do con người gây ra.

Cấu tạo các trường dữ liệu của mỗi node:
+ Hash: Giá trị dùng để **chứng nhận 1 block**. Một giá trị hash sẽ tương ứng với một bộ dữ liệu của block. Khi 1 bit trong block thay đổi thì giá trị được lập trình hash tính ra được sẽ thay đổi theo.
+ Previous Block Hash: Giá trị hash của block trước nó (Block đầu tiên sẽ có giá trị bằng 0)
+ Data: Dữ liệu lưu trong block như transaction đối với tiền ảo, thông tin về hệ thống ứng dụng
+ Timestamp: Thời gian node được sinh ra

![](https://lh3.googleusercontent.com/PTtQeZHjgOT5YoYe6fKbtLISLN0PyX5upBPjPCuZqkPyziF8TTGJ0aXdHnUE8EhFmWrL-U81I5H3KMa2rpJnYJEyE05qhXH-IEqzWQGtBn0sE7XWDT0rbin60pz3EYVgIQ6aP0NbeQQ43gf7VodFzpCMlRtWyPIUQzMw28z93_Nj5jU9qiDS2nSpQIxGjc_G0O5og6E9US_fVPGx0UAnSSk25WB-CrxFjW9Xn5ihM6EiXK3dRPZ4XgYiTbcJu-FD-sREQlSqOAK4GLhL7ctmMtj9qJSjWppE3fWy298j8R3WS6XvdXsXki7hjzBnSHVdeipbftAvjGH6ND9ePYiUY7J0ol_rxC8DlAKu3OoiqDBQ7P5PDjSpHlrXfaJNA_0OsAjO1bBhgY4op-N42yb6Ewf-13Q6C8g8FkwgO9190yx7DWPIzyTpRadFISDHp5MIc0QVAKFJUHMgiFeTfwCy8bYfHUIzyFMOwapUnpQRdn0PXOgqmFtDP_F6YVX1NZRUgahr44Hwqo4brC8mK-N00OmQYFKuBqHmDCQOTInKQPNb8PkpfZjhhXOgQnCZfpfXsoI_yW-iAcUGC2uXajnEDurqi0BpvPK6AU4qCdVRCEuMl_RIzaEoosslF9-vjsQLX96zxxUiklWQcu5ix9p_1qWpU9Api91z23A4is9HlWZ1ya94o3jkDhoGTIwRN4znVUxycfv_FH0gWs8KSw=w1000-h395-no)

```go
package main

import (
	"bytes"
	"crypto/sha256"
	"strconv"
	"time"
  	"sync"
)

// Structure of each block or node in network 
type Block struct {
	Hash          []byte
	PrevBlockHash []byte
	Data          []byte
	Timestamp     int64
}

// Define the series block in network P2P system 
type Blockchain struct {
	blocks []*Block
}

// Define a hash of each the block or node in Blockchain
func (b *Block) SetHash() {
	bTimeStamp := []byte(strconv.FormatInt(b.Timestamp, 10))
	blockAsBytes := bytes.Join([][]byte{b.PrevBlockHash, b.Data, bTimeStamp}, []byte{})
	hash := sha256.Sum256(blockAsBytes)
	b.Hash = hash[:] // stored all value of array hash in b.Hash
}

// Define a new block in P2P network
func NewBlock(data string, prevBlockHash []byte) *Block {
	block := &Block{[]byte{}, prevBlockHash, []byte(data), time.Now().Unix()}
	block.SetHash()
	return block
}

// Generate the genesis block
func NewGenesisBlock(starting string) *Block {
	return NewBlock(starting, []byte{})
}
// Initialization of the blockchain
var instantiated *Blockchain
// Asynchronous function for process the data only one time in golang 
var once = new(sync.Once)

func InitBlockchain() *Blockchain {
	once.Do(func() {
		instantiated = &Blockchain{[]*Block{NewGenesisBlock("Genesis block")}}
	})
	return instantiated
}

// Add a new block into blockchain network
func (bc *Blockchain) AddBlock(data string) {
	// Blockchain hash of the previous block
	prevBlock := bc.blocks[len(bc.blocks) - 1]
	newBlock := NewBlock(data, prevBlock.Hash)
	bc.blocks = append(bc.blocks, newBlock)
}

func main() {
	bc := InitBlockchain()

	bc.AddBlock("A send 1 btc to B")
	bc.AddBlock("B send 2 btc to C")
	bc.AddBlock("D send 1 btc to E")

	fmt.Println()
	for _, block := range bc.blocks {
		fmt.Printf("Hash : %x\n", block.Hash)
		fmt.Printf("Data : %s\n", block.Data)
		fmt.Printf("Timestamp : %x\n", block.Timestamp)
		fmt.Printf("Previous Hash : %x\n", block.PrevBlockHash)
		fmt.Println()
	}
}

```
## Introduction the Blockchain System Platform
## The Applied in Blockchain
Một số startup, platform và open source hỗ trợ bạn xây dựng cơ sở hạ tầng lưu thông Blockchain. Cái đầu tiên và quan trọng nhất là năng lực tài chính, năng lực sáng tạo trong việc vận hành các sản phẩm trong thị trường toàn cầu. Người có năng lực sáng tạo (Bloom's level 6) thì trong vòng **hai tháng** cũng có thể xây dựng cơ đồ cho bạn nếu như công ty bạn biết dùng người tài phù hợp. Không thiếu những công ty trên Thế giới xây dựng mọi thứ như Google, Microsoft, Amazon, IBM, Facebook, Tesla,...

+ Công nghệ ô tô Automotive (Automotive)
+ Chế tạo (Manufacturing)
+ Công nghệ, truyền thông và viễn thông (Tech, media & Telecommunications)
+ Dịch vụ tài chính (Financial Services)
+ Nghệ thuật & Giải trí (Art & Recreation)
+ Chăm sóc sức khỏe (Healthcare)
+ Bảo hiểm (Insurance)
+ Bán lẻ (Retail)
+ Giáo dục (Education)
+ Khu vực công (Public Sector)
+ Bất động sản (Property)
+ Nông nghiệp (Agricultural)
+ Khai thác (Mining)
+ Vận tải và Logistics (Transport & Logistics)
+ Công trình hạ tầng kỹ thuật (Utility)
+ Hợp đồng thông minh (Smart Contract)
+ Internet vạn vật (IoT)
+ Điện toán đám mây (Cloud Computing)
+ Công nghệ thông tin (Information Technology)
+ Kiến trúc (Architecture)
+ Quản lý đô thị (Urban management)
+ Chế tạo thiết bị máy bay (Flight instruments)
+ Hàng không vũ trụ (Aerospace)
+ Khoa học tự nhiên (Natural Science)
+ Khoa học xã hội (Social Science)

Tôi xin điểm sơ qua vài platform, open source, startup trong nghiên cứu Blockchain:

+ [BigChainDB](https://www.bigchaindb.com/): Là một cơ sở dữ liệu blockchain có thể mở rộng, nó được thiết kế để merge cơ sở dữ liệu truyền thống và cơ sở dữ liệu trên blockchain. 
+ [Chain Core](https://chain.com/docs/1.2/core/get-started/introduction), là phần mềm quản lý, cấp quyền và điều hành các cơ sở hạ tầng trong hệ thống Blockchain
+ [Corda](https://www.corda.net/): Corda là một dự án nguồn mở được xây dựng trên mạng blockchain, cho phép các doanh nghiệp giao dịch trong sự riêng tư nghiêm ngặt. Nền tảng không được hỗ trợ bởi bất kỳ tiền điện tử nào . Như vậy, nó không đòi hỏi sự đồng thuận kiểu khai thác.
+ [Credits](https://credits.com/vi/Home/Index), là một nền tảng blockchain mở và là một framework xây dựng các hợp đồng thông minh tự động và tiền số nội bộ. 
+ [Domus Tower Blockchain](https://pdfs.semanticscholar.org/559c/38d908521cd0772aa070619bf93a706c011e.pdf)
+ [Elements Blockchain Platform](https://elementsproject.org/)
+ [Eris:db](https://github.com/vulcanize/eris-db)
+ [Ethereum](https://ethereum.org/)
+ [HydraChain](https://github.com/HydraChain/hydrachain)
+ [Hyperledger Fabric](https://hyperledger-fabric.readthedocs.io/en/release-1.3/): Hỗ trợ quản lý một hoặc nhiều mạng, mỗi mạng quản lý các Tài sản, Thỏa thuận và Giao dịch khác nhau giữa các node nội bộ thành viên khác nhau.
+ [Hyperledger Iroha](https://www.hyperledger.org/projects/iroha): Là một hệ thống sổ cái Blockchain phân tán được đơn giản hóa và được mô đun hóa. Là một trong những dự án lớn của [Hyperledger](https://www.hyperledger.org/) thuộc [The Linux Foundation](https://www.linuxfoundation.org/), tập trung vào phát triển ứng dụng di động.
+ [Hyperledger Sawtooth Lake](https://sawtooth.hyperledger.org/docs/core/releases/0.7/introduction.html)
+ [Multichain](https://www.multichain.com/)
+ [Openchain](https://www.openchain.org/): Là một hệ thống sổ cái phân tán mã nguồn mở trong phát hành và quản lý tài sản kỹ thuật số.
+ [Quorum](https://www.jpmorgan.com/global/Quorum): Là nền tảng mã nguồn mở Blockchain, smart contract dựa trên Ethereum thuộc sở hữu của hãng tài chính lâu đời nhất của Mỹ [JPMorgan Chase](https://www.jpmorganchase.com/)  
+ [Stellar](https://www.stellar.org/): Là một cơ sở hạ tầng thanh toán phân tán, mã nguồn mở, kết nối các ngân hàng, hệ thống thanh toán và mọi người. Stellar cho phép xây dựng ví di động, công cụ thanh toán ngân hàng, thiết bị thông minh. Nó cung cấp các máy chủ API RESTful HTTP tên là Horizon, kết nối với Stellar Core, xương sống của mạng Stellar.
+ [Symbiont Assembly](https://symbiont.io/technology/)
## Build own Blockchain Platform
Blockchain là một cơ sở dữ liệu phân tán với một bộ quy tắc để xác minh các bổ sung mới cho cơ sở dữ liệu mạng hệ thống. Có nhiều cách xây dựng mạng lưới Blockchain:
### Forking những codebase đã tồn tại phát triển tiếp như [Bitcoind](https://en.bitcoin.it/wiki/Bitcoind#Initialization_and_Startup/), [bcoin](http://bcoin.io/), [btcd](https://github.com/btcsuite/btcd/blob/master/docs/README.md)

#### Compiler Bitcoin Source code in AWS

```java
# Update & Upgrade the System
sudo apt-get update
sudo apt-get upgrade

# Install dependencies there might be more based on your system
# However below instructions are for the fresh Ubuntu install/server
# Please carefully watch the logs because if something could not be install
# You have to make sure it is installed properly by trying the command or that particular
# dependency again

sudo apt-get install build-essential libtool autotools-dev autoconf pkg-config libssl-dev
sudo apt-get install libboost-all-dev
sudo apt-get install libqt5gui5 libqt5core5a libqt5dbus5 qttools5-dev qttools5-dev-tools libprotobuf-dev protobuf-compiler
sudo apt-get install libqrencode-dev autoconf openssl libssl-dev libevent-dev
sudo apt-get install libminiupnpc-dev

# Download Bitcoin Source code
# ----------------------------
cd ~
git clone https://github.com/bitcoin/bitcoin.git

# Bitcoin uses the Berkley DB 4.8
# We need to install it as well
# Download & Install Berkley DB
# -----------------------------
cd ~
mkdir bitcoin/db4/
wget 'http://download.oracle.com/berkeley-db/db-4.8.30.NC.tar.gz'
tar -xzvf db-4.8.30.NC.tar.gz
cd db-4.8.30.NC/build_unix/
../dist/configure --enable-cxx --disable-shared --with-pic --prefix=/home/theusername/bitcoin/db4/
make install

# Compile Bitcoin with Berkley DB 4.8
# -----------------------------------
cd ~/bitcoin/
./autogen.sh
# below command ./configure may return with error for dependencies
# you need to make sure that it returns with no error
# If it does please install the dependencies and rerun the /autogen.sh command again and then below command again
./configure LDFLAGS="-L/home/theusername/bitcoin/db4/lib/" CPPFLAGS="-I/home/theusername/bitcoin/db4/include/"

# below command may take 5-10 minutes based on your system
make -s -j5

# If all went well you will be able to access the binary at below location
cd ~/bitcoin/
./src/bitcoind
./src/bitcoin-qt
./src/bitcoin-cli
```
### Blockchain framework như [Solc](https://blockgeeks.com/introduction-to-solidity-part-1/), [Ether Scripter](https://etherscripter.com/0-5-1/), [Embark](https://embark.status.im/), [Tierion](https://tierion.com/), [Coinbase’s API](https://developers.coinbase.com/), [Mist](https://subscription.packtpub.com/book/big_data_and_business_intelligence/9781787122147/2/ch02lvl1sec32/mist), [Microsoft Blockchain as a Service](https://youtu.be/gQYMRxQiSwk), [Cosmos SDK](https://cosmos.network/developers)

```js
// app.js
let lotion = require('lotion');
let app = lotion({
 initialState: {
  count: 0
 }
})

let stateFunc = (state, tx) => {
	if (state.count === tx.aou)
	{
		state.count++;
	}
}
app.use(stateFunc);
app.listen(3000);
```
```
node app.js // tab 1
curl http://localhost:3000/state // tab 2
{"count":0}
curl http://localhost:3000/txs -d '{aou:0}' | jsonpp // tab 2
curl http://localhost:3000/state // tab 2
{"count":1}
```
### Blockchain Open Source:

Có 3 loại Blockchain:

+ Public blockchain như [Bitcoin](https://www.bitcoin.com/), chúng được mở cho công chúng tham gia ở mọi cấp độ và có mã nguồn mở mà cộng đồng duy trì.
+ Permissioned blockchains như [Ripple](https://ripple.com/) là các chuỗi khối được cấp quyền hoạt động trong hệ thống như giao dịch ngân hàng, thanh toán hóa đơn, quản lý tài sản kỹ thuật số,.... Chúng được sử dụng trong các hệ thống phân tán lớn. Source code của họ có thể hoặc không là nguồn mở.
+ Private Blockchain:Thành viên của họ được kiểm soát chặt chẽ. Những loại blockchains này được ưa chuộng bởi các tập đoàn có thành viên đáng tin cậy và trao đổi thông tin bí mật.

#### Một số mã nguồn mở xây dựng các loại Blockchain
Tôi sẽ giải quyết hết việc cài đặt và phát triển Blockchain trên những nền tảng phổ biến dưới đây
##### Ethereum
##### Hyperledger
##### Corda
##### HydraChain
##### MultiChain
##### Openchain
##### Elements
##### Quorum

## Build own Bitcoin Platform
## Blockchain Company & capacity of competition
