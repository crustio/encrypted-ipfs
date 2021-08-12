# encrypted-ipfs
encrypted-ipfs aims at building a safe and high usability IPFS based on original go-ipfs. There are two parts of it: modified IPFS and encryptor. In order to support new encrypted IPFS, there are some changes of the original one to adapt to new encryption related features. Encryptor is an another program to do encryption. Any process which implements indicated API can play as an encryptor. In this way can developers customize their own encryptor for different purpose. You can refer to Crust sWorker to know more about what an encryptor looks like.
For the encrypted IPFS there are six related repos which are go-ipfs-encryptor, go-ipfs, go-ipfs-pinner, go-merkledag, go-ipfs-blockstore and go-ipfs-config. Especially, go-ipfs-encryptor is maintained by Crust while others are go-ipfs projects. For go-ipfs-encryptor Crust adds basic functions to let it be a bridge between a customized encryptor and origin IPFS. For the rest repos Crust tries its best to modify less code to let developers build an encrypted IPFS from the original one.

## go-ipfs-encryptor
go-ipfs-encryptor works with an encrytor(Crust sWorker in Crust network). It provides interfaces for go-ipfs related repos to build an encrypted IPFS. Seal and unseal are the two important functions. Seal is used to encrypt file block which can be stored to anywhere.
Seal function will encrypt file block data and return a path which indicates the real storage path of the encrypted data. This path is treated as the index of file block. In this way IPFS database doesn’t need to store the real data any more. As we know, IPFS doesn’t support multi-path. When IPFS original path disk has no more space, data migration has to be planed. This brings a big inconvenience for users. But with go-ipfs-encryptor IPFS can store real data to different disks while IPFS original path only maintains index data. An experimental result shows that 1TB file data just produces about 1GB index data, which means in this way IPFS can be scaled up to support multi-path.
Unseal function recieves index path as parameter and returns decrypted file block.

## go-ipfs related projects
There are five go-ipfs project modified for encrypted IPFS. We will describe the changes of each project. You can check the change details in Crust repos.

### go-ipfs
Add Crust configure as a global flag. When IPFS starts up, it will decide whether to run in encrypted mode through the Crust configure.

### go-ipfs-pinner
go-ipfs will invoke functions in go-ipfs-pinner. The latter will check if there needs to invoke seal function to seal file, if need, it will set seal information in context.

### go-merkledag
go-ipfs-pinner will invoke functions in go-merkledag. If seal flag can be parsed from context, related flag will be set for seal later.

### go-ipfs-blockstore
Modify IPFS block cache mechanism to adapt to seal.

### go-ipfs-config
Add Crust related configure and set leveldb as the defaut database.
