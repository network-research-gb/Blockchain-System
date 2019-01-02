# Blockchain System

![](https://hyperledger.org/wp-content/uploads/2018/07/Hyperledger_Greenhouse-59-2.png)

## Introduction about Blockchain System
Blockchain là một công nghệ cho phép truyền dữ liệu an toán với các chính sách và hệ thống mã hóa vô cùng phức tạp, tương tự cuốn sổ cái kế toán của một công ty, nơi mà tiền mặt và mọi hoạt động của công ty được giám sát chặt chẽ. Trong trường hợp này Blockchain là một cuốn sổ cái kế toán hoạt động trong lĩnh vực kỹ thuật số. Hay nói một cách khác Blockchain là một cơ sở dữ liệu trong một mạng và được chia sẻ cho những người tham gia vào mạng lưới

Blockchain sở hữu tính năng là việc truyền tải dữ liệu (bất cứ cái gì trên đời cũng có thể là dữ liệu) không đòi hỏi một trung gian để xác nhận thông tin. Hệ thống Blockchain tồn tại rất nhiều nút độc lập có khả năng xác thực thông tin mà không đòi hỏi **dấu hiệu của niềm tin**. **Thông tin trong Blockchain không thể bị thay đổi và chỉ được bổ sung thêm khi có sự đồng thuận của tất cả các nút trong hệ thống**. **Người tham gia vào mạng có trách nhiệm phê duyệt các giao dịch và giám sát mạng bằng cách giải quyết các công thức tinh vi với sự trợ giúp của máy tính**. Nó là một hệ thống ngang hàng P2P, loại bỏ tất cả mọi khâu trung gian, làm tăng cường an ninh, minh bạch và sự ổn định cũng như giảm thiểu chi phí và lỗi do con người gây ra.

Cấu tạo các trường dữ liệu của mỗi node:
+ Hash: Giá trị dùng để **chứng nhận" 1 block**. 1 giá trị hash sẽ tương ứng với 1 bộ dữ liệu của block. Khi 1 bit trong block thay đổi thì giá trị được lập trình hash tính ra được sẽ thay đổi theo.
+ Previous Block Hash: Giá trị hash của block trước nó (Block đầu tiên sẽ có giá trị bằng 0)
+ Data: Dữ liệu lưu trong block như transaction đối với tiền ảo, thông tin về hệ thống ứng dụng
+ Timestamp: Thời gian node được sinh ra

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

// Define a hash in block
func (b *Block) SetHash() {
	bTimeStamp := []byte(strconv.FormatInt(b.Timestamp, 10))
	blockAsBytes := bytes.Join([][]byte{b.PrevBlockHash, b.Data, bTimeStamp}, []byte{})
	hash := sha256.Sum256(blockAsBytes)
	b.Hash = hash[:] // stored all value of array hash in b.Hash
}

// Define a new block in network
func NewBlock(data string, prevBlockHash []byte) *Block {
	block := &Block{[]byte{}, prevBlockHash, []byte(data), time.Now().Unix()}
	block.SetHash()
	return block
}

// Genesis Block
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
## Introduction about Blockchain System Platform
## The Applied in Blockchain 
## Build own Blockchain Platform
## Build own Blockchain Bitcoin Platform
## Blockchain Company
