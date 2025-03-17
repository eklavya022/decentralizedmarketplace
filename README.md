Decentralized Marketplace for Services
Project Title
Decentralized Marketplace for Services - A platform for freelancers to offer services with decentralized payment and reputation systems.

Project Description
This project is a blockchain-powered decentralized marketplace where freelancers can list their services, and clients can securely hire them using smart contracts. The platform ensures fair and transparent transactions by utilizing an escrow system, where funds are only released upon service completion. Additionally, a reputation system allows clients to leave feedback, ensuring trust and quality assurance.

Project Vision
The vision of this project is to create a fair and transparent ecosystem for freelancers and clients. By eliminating intermediaries, it aims to provide cost-effective solutions and secure transactions, ensuring freelancers receive fair compensation for their work.

Future Scope
Implementation of an escrow smart contract for dispute resolution.
Integration with decentralized identity verification systems.
Multi-currency and crypto wallet support.
AI-driven job recommendations for freelancers and clients.
Decentralized arbitration for resolving disputes.
Key Features
Service Listing: Freelancers can list their services with a description and price.
Secure Payment System: Funds are held in escrow and released upon service completion.
Transparent Review System: Clients can rate and review completed services.
Direct Transactions: No intermediaries, ensuring fair payment distribution.
Smart Contract Execution: Trustless agreements between freelancers and clients.
Smart Contract Code
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.9;

contract DecentralizedMarketplace {
    struct Service {
        uint256 id;
        address freelancer;
        address buyer;
        string description;
        uint256 price;
        bool available;
        bool completed;
    }

    struct Review {
        address reviewer;
        uint8 rating;
        string comment;
    }

    uint256 private nextServiceId;
    mapping(uint256 => Service) public services;
    mapping(uint256 => Review[]) public serviceReviews;
    mapping(address => uint256[]) public freelancerServices;

    event ServiceListed(uint256 indexed id, address indexed freelancer, string description, uint256 price);
    event ServicePurchased(uint256 indexed id, address indexed buyer, uint256 price);
    event ServiceCompleted(uint256 indexed id, address indexed freelancer, address indexed buyer);
    event ReviewAdded(uint256 indexed serviceId, address indexed reviewer, uint8 rating, string comment);

    function listService(string memory description, uint256 price) external {
        require(price > 0, "Price must be greater than zero");
        services[nextServiceId] = Service(nextServiceId, msg.sender, address(0), description, price, true, false);
        freelancerServices[msg.sender].push(nextServiceId);
        emit ServiceListed(nextServiceId, msg.sender, description, price);
        nextServiceId++;
    }

    function purchaseService(uint256 id) external payable {
        Service storage service = services[id];
        require(service.available, "Service not available");
        require(msg.value == service.price, "Incorrect price");
        require(service.buyer == address(0), "Service already purchased");

        service.buyer = msg.sender;
        service.available = false;
        emit ServicePurchased(id, msg.sender, service.price);
    }

    function completeService(uint256 id) external {
        Service storage service = services[id];
        require(service.buyer == msg.sender, "Only the buyer can mark service as completed");
        require(!service.completed, "Service already completed");

        payable(service.freelancer).transfer(service.price);
        service.completed = true;
        emit ServiceCompleted(id, service.freelancer, service.buyer);
    }

    function addReview(uint256 serviceId, uint8 rating, string memory comment) external {
        Service storage service = services[serviceId];
        require(service.completed, "Service must be completed to leave a review");
        require(msg.sender == service.buyer, "Only the buyer can leave a review");
        require(rating >= 1 && rating <= 5, "Rating must be between 1 and 5");

        serviceReviews[serviceId].push(Review(msg.sender, rating, comment));
        emit ReviewAdded(serviceId, msg.sender, rating, comment);
    }
}

![image](https://github.com/user-attachments/assets/404c991d-9348-4c22-a008-71d160fef24c)

