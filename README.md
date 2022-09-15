# Private Network
Bài viết dựa và Tutorial
https://docs.substrate.io/tutorials/v3/private-network/

Các bước để tạo 1 Private Network:
1. Tạo key cho nodeboot và validator
2. Tạo chain spec
3. Convert chain sang raw format

## Bước 1: Tạo key cho nodeboot và validator
Cách tạo Key:
Có 1 số cách tạo Key:
1. Sử dụng node-template
2. Sử dụng Subkey: https://docs.substrate.io/v3/tools/subkey/
3. Polkadot-JS application
4. Third-part generation

Hướng dẫn tạo Key sử dụng node-template

Tạo bộ khoá thứ nhất
Bước 1:
Tạo 1 cụm "secret phrase" ngẫu nhiên và keys sử dụng dòng lệnh:
./target/release/node-template key generate --scheme Sr25519 --password-interactive

--> yêu cầu nhập mật khẩu, giống như tạo ví trên Metamask

Kết quả trả về:

Secret phrase:     sudden animal photo diet race stove pitch debris boring network early poverty

Network ID:        substrate

Secret seed:       0x92d0ab5d4581f8c7acef645054a85ecdca3bdfb3e412e7bd2f8ffbbd0abede00

Public key (hex):  0x222c8f856cdd057715673f8c0fd06210ec26eca4cca0ad898af4ec22cd97791e

Account ID:        0x222c8f856cdd057715673f8c0fd06210ec26eca4cca0ad898af4ec22cd97791e

Public key (SS58): 5CqWirnUrH1epzdttJvh7VuYafASmK4rd2FqNLYH3ss8WP4k

SS58 Address:      5CqWirnUrH1epzdttJvh7VuYafASmK4rd2FqNLYH3ss8WP4k

Khoá công khai Sr25519: 5CqWirnUrH1epzdttJvh7VuYafASmK4rd2FqNLYH3ss8WP4k sẽ sử dụng cho Aura consensus

Bước 2:
Sử dụng "Secret phrase" ở trên để tạo key sử dụng Ed25519:

./target/release/node-template key inspect --password-interactive --scheme Ed25519 "sudden animal photo diet race stove pitch debris boring network early poverty"

Kết quả:

Secret phrase:     sudden animal photo diet race stove pitch debris boring network early poverty

Network ID:        substrate

Secret seed:       0x92d0ab5d4581f8c7acef645054a85ecdca3bdfb3e412e7bd2f8ffbbd0abede00

Public key (hex):  0xbe599ca036673f54810b9bf703bc65a5d4fa32a7e52dc3c254546afd744e8ed3

Account ID:        0xbe599ca036673f54810b9bf703bc65a5d4fa32a7e52dc3c254546afd744e8ed3

Public key (SS58): 5GNHaLKWFU84H89p6iq6euYfwQHNTdY7UPzcPRd6Puc7hizV

SS58 Address:      5GNHaLKWFU84H89p6iq6euYfwQHNTdY7UPzcPRd6Puc7hizV

Khoá công khai Ed25519: 5GNHaLKWFU84H89p6iq6euYfwQHNTdY7UPzcPRd6Puc7hizV sẽ sử dụng cho Granpa consensus

Tóm lại có 2 bộ khoá cho node thứ nhất:

Aura: 5CqWirnUrH1epzdttJvh7VuYafASmK4rd2FqNLYH3ss8WP4k \
Granpa: 5GNHaLKWFU84H89p6iq6euYfwQHNTdY7UPzcPRd6Puc7hizV

Tương tự tạo bộ khoá thứ 2:

Aura-Sr25519

Secret phrase:     duty wreck battle cupboard sound uniform season veteran risk expose wide stairs

Network ID:        substrate

Secret seed:       0x03c2e7817c760853790ced01dc1e3b762f33784fd695890aa8e4ec229f5402ba

Public key (hex):  0x96cace658962639c966dde363bc320cb0ae022400101e74d84e1728df89d3507

Account ID:        0x96cace658962639c966dde363bc320cb0ae022400101e74d84e1728df89d3507

Public key (SS58): 5FURHY2f91ztNHi1svWNRvoTR8XP3FxWMBF1utmpwb2UvzA6

SS58 Address:      5FURHY2f91ztNHi1svWNRvoTR8XP3FxWMBF1utmpwb2UvzA6

Granpa-Ed25519

Secret phrase:     duty wreck battle cupboard sound uniform season veteran risk expose wide stairs

Network ID:        substrate

Secret seed:       0x03c2e7817c760853790ced01dc1e3b762f33784fd695890aa8e4ec229f5402ba

Public key (hex):  0x21b3498f34ff101d6e8eac9a81bf3f6fbed4fd2d81e03a2eb17bf9ffb47f4464

Account ID:        0x21b3498f34ff101d6e8eac9a81bf3f6fbed4fd2d81e03a2eb17bf9ffb47f4464

Public key (SS58): 5CpthNMkJHEz5ctKDWmjRbZ3wZttRsDfjTBpkHBJYRquAvmj

SS58 Address:      5CpthNMkJHEz5ctKDWmjRbZ3wZttRsDfjTBpkHBJYRquAvmj

Node thứ 2:

Aura: 5FURHY2f91ztNHi1svWNRvoTR8XP3FxWMBF1utmpwb2UvzA6

Granpa: 5CpthNMkJHEz5ctKDWmjRbZ3wZttRsDfjTBpkHBJYRquAvmj

Trong bài này chạy 2 Node nên chỉ cần tạo 2 bộ khoá

## Bước 2. Tạo chain spec
Tạo custom chain specification

Sau khi tạo các khoá ở trên, bạn cần tạo custom chain spec sử dụng những key ở trên cho validator

Tạo 1 chain spec dựa vào chain spec hiện có.

Tạo file customSpec.json:

./target/release/node-template build-spec --disable-default-bootnode --chain local > res/customSpec.json

Sửa 1 số thông tin của file customSpec.json:
1. Thông tin chain:
   "name": "DuongHB Node",
   "id": "duonghb_testnet",
   "chainType": "DuongHB",

2. Thông tin khoá Aura, Granpa cho validator(lấy 2 Key đã Gen ở trên):
"aura": {
   "authorities": [
   "5CqWirnUrH1epzdttJvh7VuYafASmK4rd2FqNLYH3ss8WP4k",
   "5FURHY2f91ztNHi1svWNRvoTR8XP3FxWMBF1utmpwb2UvzA6"
   ]
},
"grandpa": {
   "authorities": [
   [
   "5GNHaLKWFU84H89p6iq6euYfwQHNTdY7UPzcPRd6Puc7hizV",
   1
   ],
   [
   "5CpthNMkJHEz5ctKDWmjRbZ3wZttRsDfjTBpkHBJYRquAvmj",
   1
   ]
   ]
},

Lưu ý ở phần grandpa có 2 giá trị key và số 1: phần key thì ta đã biết là key granpa được sinh ra ở trên còn số 1 là trọng
số(weighted votes) của validator, trọng số càng cao thì validator đó càng có nhiều ảnh hưởng.
Lưu ý: đảm bảo rằng key aura và grandpa sử dụng ở trên là duy nhất cho mỗi validator.
Nếu nó bị trùng lặp sẽ xảy ra xung đột

## Bước 3. Convert chain sang raw format
Sau khi bạn thay đổi thông tin của customSpec.json, bạn "phải" convert nó sang raw spec trước khi nó có thể dùng.
Raw spec bao gồm thông tin tương tự như chain spec chưa được chuyển đổi.
Tuy nhiên, raw spec cũng chứa các khóa lưu trữ được mã hóa mà nút sử dụng
để tham chiếu dữ liệu trong bộ nhớ cục bộ của nó.
Thực hiện convert:

./target/release/node-template build-spec --chain=res/customSpec.json --raw --disable-default-bootnode > res/customSpecRaw.json

Sau đó chia sẻ file customSpecRaw.json với các người khác

## Bước 4. Chạy Private Network

### Chạy Node đầu tiên:
./target/release/node-template \
--base-path /tmp/node01 \
--chain ./res/customSpecRaw.json \
--port 30333 \
--ws-port 9945 \
--rpc-port 9933 \
--validator \
--rpc-methods Unsafe \
--name MyNode01

Lưu ý các dòng lệnh sau:
1. --base-path
2. --chain
.......

### Thêm key và keystore:
Sau khi bạn bắt đầu nút đầu tiên, không có khối nào được tạo ra.
Bước tiếp theo là thêm hai loại khóa vào kho khóa cho mỗi nút trong mạng.
Để node có thể xác định đó đúng là bạn

./target/release/node-template key insert --base-path /tmp/node01 \
--chain res/customSpecRaw.json \
--scheme Sr25519 \
--suri "sudden animal photo diet race stove pitch debris boring network early poverty" \
--password-interactive \
--key-type aura

./target/release/node-template key insert --base-path /tmp/node01 \
--chain res/customSpecRaw.json \
--scheme Ed25519 \
--suri "sudden animal photo diet race stove pitch debris boring network early poverty" \
--password-interactive \
--key-type gran

./target/release/node-template key insert --base-path /tmp/node02 \
--chain res/customSpecRaw.json \
--scheme Sr25519 \
--suri "duty wreck battle cupboard sound uniform season veteran risk expose wide stairs" \
--password-interactive \
--key-type aura

./target/release/node-template key insert --base-path /tmp/node02 \
--chain res/customSpecRaw.json \
--scheme Ed25519 \
--suri "duty wreck battle cupboard sound uniform season veteran risk expose wide stairs" \
--password-interactive \
--key-type gran



Sau khi tạo ra thì trong thư mục: ls /tmp/node01/chains/duonghb_testnet/keystore sẽ chứa
thông tin 2 key

### Cho phép những người khác tham gia
./target/release/node-template \
--base-path /tmp/node02 \
--chain ./res/customSpecRaw.json \
--port 30334 \
--ws-port 9946 \
--rpc-port 9934 \
--validator \
--rpc-methods Unsafe \
--name MyNode02 \
--bootnodes /ip4/127.0.0.1/tcp/30333/p2p/12D3KooWMJuJK8Fd1z9bt8m43LVgyJTJUqUpn8C77LzjrFsBSAEa \
--password-interactive

Chú ý phần thay thế phần: Local node identity is: 12D3KooWFPStyyw1E4caTajLwNmqSaUKsuhz4xnRncqfRVH7UPfd
