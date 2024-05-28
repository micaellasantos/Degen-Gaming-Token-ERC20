# Create and Mint Token
Creating and minting a token involves deploying a smart contract on a blockchain platform like Ethereum.

## Description
Creating and minting tokens involves gas fees (transaction fees) and requires some familiarity with blockchain development and smart contracts. 

## Getting Started

### Executing the Program

Using Remix IDE, a online development environment for Ethereum smart contracts, execute the provided Solidity smart contract by creating a new file, pasting the code, compiling it using the Solidity Compiler tab, deploying it to a chosen Ethereum network via the Deploy & Run Transactions tab, and subsequently interacting with its functions, allowing us to test and validate the contract's behavior comprehensively.

Code:

# Create and Mint Token
Creating and minting a token involves deploying a smart contract on a blockchain platform like Ethereum.

## Description
Creating and minting tokens involves gas fees (transaction fees) and requires some familiarity with blockchain development and smart contracts. 

## Getting Started

### Executing the Program

Using Remix IDE, a online development environment for Ethereum smart contracts, execute the provided Solidity smart contract by creating a new file, pasting the code, compiling it using the Solidity Compiler tab, deploying it to a chosen Ethereum network via the Deploy & Run Transactions tab, and subsequently interacting with its functions, allowing us to test and validate the contract's behavior comprehensively.

Code:

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.18;

import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
import "./Ownable.sol";

contract DegenGamingToken is ERC20, Ownable {

    struct Item {
        uint256 itemId;
        string name;
        uint256 price;
        uint256 quantity;
    }

    Item[] public items;
    mapping(uint256 => uint256) private itemIdToIndex;
    uint256 private nextItemId;

    event ItemAdded(uint256 indexed itemId, string name, uint256 price, uint256 quantity);
    event ItemRedeemed(address indexed user, uint256 indexed itemId, uint256 price);
    event Debug(string message, uint256 value);

    constructor(address owner) ERC20("DegenGamingToken", "DGT") Ownable(owner) {
        nextItemId = 1; 
    }

    function mint(address to, uint256 amount) public onlyOwner {
        _mint(to, amount);
    }

    function burn(uint256 amount) public {
        _burn(msg.sender, amount);
    }

    function transferTokens(address to, uint256 amount) public returns (bool) {
        _transfer(msg.sender, to, amount);
        return true;
    }

    function redeem(uint256 amount) public {
        _burn(msg.sender, amount);
    }

    function addItem(string memory name, uint256 price, uint256 quantity) public onlyOwner {
        uint256 itemId = nextItemId; // Use nextItemId for the new item
        items.push(Item(itemId, name, price, quantity));
        itemIdToIndex[itemId] = items.length - 1;
        emit ItemAdded(itemId, name, price, quantity);
        nextItemId++; // Increment nextItemId for the next item
    }

    function redeemItem(uint256 itemId) public {
        uint256 index = itemIdToIndex[itemId];
        require(index < items.length, "Item does not exist");
        emit Debug("Index", index);

        Item storage item = items[index];
        emit Debug("Quantity", item.quantity);
        require(item.quantity > 0, "Item out of stock");

        emit Debug("Balance", balanceOf(msg.sender));
        emit Debug("Item Price", item.price);
        require(balanceOf(msg.sender) >= item.price, "Insufficient token balance");

        _burn(msg.sender, item.price);
        item.quantity -= 1;

        emit ItemRedeemed(msg.sender, itemId, item.price);
    }

    function getItem(uint256 itemId) public view returns (uint256 id, string memory name, uint256 price, uint256 quantity) {
        uint256 index = itemIdToIndex[itemId];
        require(index < items.length, "Item does not exist");
        Item storage item = items[index];
        return (item.itemId, item.name, item.price, item.quantity);
    }

    function getItemCount() public view returns (uint256) {
        return items.length;
    }
}

