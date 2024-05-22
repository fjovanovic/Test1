```solidity
pragma solidity >= 0.8.0;

contract PetStore {
    struct Pet {
        uint id;
        string name;
        uint price;
        address owner;
        bool forSale;
    }
    mapping (uint => Pet) public kucniLjubimci;

    uint public petsCounter = 0;
    uint public petsForSaleCounter = 0;
    address public shopOwner;

    constructor() {
        shopOwner = msg.sender;
    }

    modifier isShopOwner() {
        require(shopOwner == msg.sender);
        _;
    }

    function addPet(string memory _name, uint price, bool forSale) external isShopOwner {
        Pet memory newPet = Pet(petsCounter, _name, price, shopOwner, forSale);
        kucniLjubimci[petsCounter] = newPet;

        petsCounter++;
        if (forSale) {
            petsForSaleCounter++;
        }
    }

    function buyPet(uint _id) external payable {
        require(_id < petsCounter);
        require(msg.value == kucniLjubimci[_id].price);
        require(kucniLjubimci[_id].forSale);

        address payable ownerPayable = payable(kucniLjubimci[_id].owner);
        bool sent = ownerPayable.send(msg.value);
        require(sent, "Failed to buy Pet");

        kucniLjubimci[_id].owner = msg.sender;
        kucniLjubimci[_id].forSale = false;
        petsForSaleCounter--;
    }

    function getPet(uint _id) external view returns (uint, string memory, uint, address, bool) {
        Pet storage pet = kucniLjubimci[_id];
        return (pet.id, pet.name, pet.price, pet.owner, pet.forSale);
    }
}
```
