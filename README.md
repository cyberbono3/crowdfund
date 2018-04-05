# crowdfund

Ethereum storage is able to store data in chain data and state trie. Writing to the chain data is more chepaer then in state trie.
Data reading is possible only from state trie.
This simple Crowdfund contract is able to notify when the new contribution is made.
To enable this functionality i declare new event 'New Contribution' in contract and in the function contribute() which is called upon new contribution reaches the contract.


## Out of gas problem
Let imagine, one controbuter takes a decision to transfer 1 Wei 1000 times. 
To make the refund beneficiary will have to initiate 1000 transfers that will cost a huge amount of gas.
Eventually, beneficiary can face "out of gas" or "exceeds gas block limit" problem.


```
contract CrowdFund {

  address public beneficiary;
  uint256 public goal;
  uint256 public deadline;
  mapping (address => uint256) funders;
  uint256 public refundIndex;
  address[] funderAdresses;
  
//  struct Funder {
//  address addr;
//  uint256 contribution;
//  }
  
//  Funder[] funders;

  event NewContribution(address indexed _from, uint256 _value);

  function CrowdFund(address _beneficiary, uint256 _goal, uint256 _duration) {
    beneficiary = _beneficiary;
    goal = _goal;
    deadline = now + _duration;
  }

  
  function getFunderContribution(address _addr) constant returns (uint) {
    return funders[_addr];
  }

  function getFunderAddress(uint _index) constant returns (address) {
    return funderAddresses[_index];
  }

  function funderAddressLength() constant returns (uint) {
    return funderAddresses.length;
  }


  // function contribute() payable {
  // funders.push(Funder(msg.sender, msg,value));
  // }


  function contribute() payable {
    if(funders[msg.sender] == 0) funderAddresses.push(msg.sender);
    funders[msg.sender] += msg.value;  
    NewContribution(msg.sender, msg.value);
  }

  function payout() {
    if(this.balance >= goal && now > deadline) beneficiary.send(this.balance);
  }
    //out of gas problem
   // function refund()  {
  //   if (msg.sender != beneficiary) throw;
   //  uint256 index = 0 //
  //   while (index < funders.length) {
  //   funders[index].send(funders[index].contribution);
 //    index++
 //    }
 //   }
 
 
 
     
// funder can request refund directly upon calling refund function, the refund function can be called only once for each funder
  function refund() {
    if(now > deadline && this.balance < goal) {
      msg.sender.send(funders[msg.sender]);
      funders[msg.sender] = 0;
    }
  }

}
```
