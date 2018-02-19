
# PyOTA "Hello world" tutorial.

The goal of this tutorial is to create a new, empty wallet based on a new random seed and to transfer IOTA tokens to it from a wallet generated via the faucet. In this tutorial the following tools are used:
 * Python 3.6.4 - probably any Python 3 will work
 * Jupyter Notebooks 5.4.0 - needed only for ipynb version of script
 * iota 2.0.4 - PyOTA module
 * requests 2.18.4 - module for handling faucet API

## Imports
Currently, we have the following imports:
 * iota - module name for [PyOTA](https://pyota.readthedocs.io/en/latest/)
 * secrets - "The [secrets](https://docs.python.org/3/library/secrets.html#module-secrets) module is used for generating cryptographically strong random numbers suitable for managing data such as passwords, account authentication, security tokens, and related secrets."
 * requests - "[Requests](http://docs.python-requests.org/en/master/) is an elegant and simple HTTP library for Python, built for human beings."


```python
import iota
import secrets
import requests
```

## Versions of modules used in tutorial:


```python
assert iota.__version__ == '2.0.4'
assert requests.__version__ == '2.18.4'
```

If above assertions will fail, remove them or adjust. They are just for module version checks.

## Connecting to the testnet
Since we would like to be as close to the actual Main IOTA network as possible, we will use the IOTA testnet. The IOTA testnet is almost identical to the mainnet but has faster confirmation times and is reset regularly, so tokens used there have no value. Here is a list of example testnet nodes:

* http://p103.iotaledger.net:14700
* https://testnet140.tangle.works:443
* http://p101.iotaledger.net:14700

Please note that the nodes are down or unresponsive at times.

## Configuration
To execute the code in this tutorial we need to define a few parameters at the beginning:
 * depth: int - Depth at which to attach the bundle. From docs: "The input value is depth, which basically determines how many bundles to go back to for finding the transactions to approve. The higher your depth value, the more "babysitting" you do for the network (as you have to confirm more transactions)."
 * uri: string - address of a testnet node in the same format as in the example above.


```python
depth = 3
uri = 'https://testnet140.tangle.works:443'
```

# Generating seeds and addresses
This part is dedicated to generating receiver and sender seeds and addresses. If you run this code from top to bottom, new pairs will be generated every time. This might not be the intended behavior. If you would like to use constant values while experimenting, re-run your code from the [**Usage** section](#Usage).

## Generating and printing a receiver seed
We can easily and securely generate seeds which will be used by the receiver side.


```python
chars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZ9'
receiver_seed = ''
for i in range(81): receiver_seed += secrets.choice(chars)
    
print(receiver_seed)
```

    PEMETCMEKOYDHZMNFATHGIXDGJQMOKSRMNWICMYDG9DBXI9XJXZQO9QMSTONMUHJHLTDBNASJNMKTVN9S



```python
api = iota.Iota(uri, seed=receiver_seed)
api.get_node_info()
```




    {'appName': 'IRI Testnet',
     'appVersion': '1.4.2.1',
     'duration': 0,
     'jreAvailableProcessors': 8,
     'jreFreeMemory': 258859936,
     'jreMaxMemory': 15271460864,
     'jreTotalMemory': 1218445312,
     'jreVersion': '1.8.0_161',
     'latestMilestone': TransactionHash(b'GPYX9MGLTYHNKHOTATTVUQEEKKSUEJYOVK9LUENWMCENNNZLPYGHIKVHROVINCZDYRZYDSHAUBBVYZ999'),
     'latestMilestoneIndex': 317631,
     'latestSolidSubtangleMilestone': TransactionHash(b'GPYX9MGLTYHNKHOTATTVUQEEKKSUEJYOVK9LUENWMCENNNZLPYGHIKVHROVINCZDYRZYDSHAUBBVYZ999'),
     'latestSolidSubtangleMilestoneIndex': 317631,
     'neighbors': 1,
     'packetsQueueSize': 0,
     'time': 1519044999136,
     'tips': 10,
     'transactionsToRequest': 0}



## Prepare receiver address
We already have our seed for the receiver (in the variable **receiver_seed**), now we would like to have the address. Let's generate one:


```python
gna_result = api.get_new_addresses(count=1)
addresses = gna_result['addresses']
receiver_address = addresses[0]
print("Receiver address 0 is: " + str(receiver_address))
```

    Receiver address 0 is: EJQE9USJIZSGHLNCRSJFVKNAVNOVHLSVHLQOXIHWEBUJKHLC9BNYLLWCQOIHOADFXJKRKXPTGXBGQ9NYZ


## Prepare sender - how to get testnet tokens?
If we are generating new wallets from new seeds then those wallets will be empty (well, [probably](https://matthewwinstonjohnson.gitbooks.io/iota-guide-and-faq/how-secure-is-my-seed.html)). Since we would like to simulate the transfer of "value" between two parties, we would need to have some tokens in at least one wallet in our setup. Thanks to IOTA devs, there is a service called a faucet which is generating seeds and wallet addresses with some testnet tokens in it (2K at the moment). They also provide a JSON API so we can use it programmatically.


```python
r = requests.get('https://seeedy.tangle.works/')
print("Faucet response: " + str(r.status_code))
sender_wallet = r.json()
```

    Faucet response: 200



```python
sender_seed = sender_wallet["seed"]
sender_address = sender_wallet["address"]
```


```python
print("Sender seed: " + sender_seed)
print("Sender address: " + sender_address)
print("Sender amount: " + str(sender_wallet["amount"]))
```

    Sender seed: QVSFINNL9PSUDVXOVUTQZFIZDNZOWRUASOBASSEJPJDGSIGAXVONJYYPQIZYRFDLANKBKYRZLSYPFWFPD
    Sender address: CENSNVDK9BUHDAOEBMUDKXLPBQ9YCTPQKWPFMYAYVVLOFOCCCLMYVNSLKUDCEHWXOUMPLFA9DFDFAIFGD
    Sender amount: 2000


# <a name="usage">Usage</a>

To this moment we should have seeds and addresses for the sender (with tokens) and receiver. They are in:
* sender_seed
* sender_address
* receiver_seed
* receiver_address

If you would like to input your own seeds and addressed you can do it in following way:
```
sender_seed = "AESZMB9SVDQBWRQXSXOCDJVAZOVGMDCPMGHJIQASJJNGYYZUSSBKXPTBAIXSFFYBPQCNAUMLKNEGAHL9X"
sender_address = "ETQFDQUQHRMO9MXPEKCZMFWULGKOBZFCAUEQRSAFQTUOQIYZM9OJAGKMIWJIGURGMDDBONFIVEBOLGQ9C"
receiver_seed = "QVWIMXF9QVFJKWOBXAGEUFYLBJH9SUZDZUPSDABOFSVTL9FCSZCRFESPZNZB9QVTLBSQZVWNTUZPWERZW"
receiver_address = "DZMTOBWDQ9DZPWGJ9BOUSCOWKJDOFSJVCIXLLSEEQDRDUVOIY9QEKE9JLBNWAPLLFTELGATRGVIIV9QYW"
```


```python
api = iota.Iota(uri, seed=sender_seed)
sender_account = api.get_account_data(start=0)
sender_account["balance"]
```




    0




```python
api = iota.Iota(uri, seed=receiver_seed)
receiver_account = api.get_account_data(start=0)
receiver_account["balance"]
```




    0



## Prepare transaction

Let's switch back to the sender seed and prepare message. Such a message is built from a string and will be attached to the transaction, so that the receiver will be able to see it easily.


```python
api = iota.Iota(uri, seed=sender_seed)
```


```python
message = ("Here, have all my testnet tokens!")
```

The proposed transaction must consist of: receiver **address** and the **value** we would like to send and the **message** which will be added to the transfer.


```python
proposedTransaction = iota.ProposedTransaction(address = iota.Address(receiver_address), 
                                               value = sender_account["balance"], 
                                               message = iota.TryteString.from_string(message)
                                              )
```


```python
proposedTransaction
```




    ProposedTransaction(**{'address': Address(b'EJQE9USJIZSGHLNCRSJFVKNAVNOVHLSVHLQOXIHWEBUJKHLC9BNYLLWCQOIHOADFXJKRKXPTGXBGQ9NYZ'),
                         'attachment_timestamp': 0,
                         'attachment_timestamp_lower_bound': 0,
                         'attachment_timestamp_upper_bound': 0,
                         'branch_transaction_hash': TransactionHash(b'999999999999999999999999999999999999999999999999999999999999999999999999999999999'),
                         'bundle_hash': None,
                         'current_index': None,
                         'hash_': None,
                         'last_index': None,
                         'legacy_tag': Tag(b'999999999999999999999999999'),
                         'nonce': Nonce(b'999999999999999999999999999'),
                         'signature_message_fragment': None,
                         'tag': Tag(b'999999999999999999999999999'),
                         'timestamp': 1519045008,
                         'trunk_transaction_hash': TransactionHash(b'999999999999999999999999999999999999999999999999999999999999999999999999999999999'),
                         'value': 0})



# Execute transaction


```python
transfer = api.send_transfer(transfers = [proposedTransaction], 
                  depth = depth,
                  inputs = [iota.Address(sender_address, key_index=0, security_level=2)]
                 ) # should work on working and synced node
```


    ---------------------------------------------------------------------------

    BadApiResponse                            Traceback (most recent call last)

    <ipython-input-16-bf31d7dbe23f> in <module>()
          1 transfer = api.send_transfer(transfers = [proposedTransaction], 
          2                   depth = depth,
    ----> 3                   inputs = [iota.Address(sender_address, key_index=0, security_level=2)]
          4                  ) # should work on working and synced node


    ~/anaconda3/envs/PyOTA/lib/python3.6/site-packages/iota/api.py in send_transfer(self, depth, transfers, inputs, change_address, min_weight_magnitude)
        904       inputs              = inputs,
        905       changeAddress       = change_address,
    --> 906       minWeightMagnitude  = min_weight_magnitude,
        907     )
        908 


    ~/anaconda3/envs/PyOTA/lib/python3.6/site-packages/iota/commands/__init__.py in __call__(self, **kwargs)
        128       self.request = replacement
        129 
    --> 130     self.response = self._execute(self.request)
        131 
        132     replacement = self._prepare_response(self.response)


    ~/anaconda3/envs/PyOTA/lib/python3.6/site-packages/iota/commands/extended/send_transfer.py in _execute(self, request)
         52       minWeightMagnitude  = min_weight_magnitude,
         53       trytes              = pt_response['trytes'],
    ---> 54       reference           = reference,
         55     )
         56 


    ~/anaconda3/envs/PyOTA/lib/python3.6/site-packages/iota/commands/__init__.py in __call__(self, **kwargs)
        128       self.request = replacement
        129 
    --> 130     self.response = self._execute(self.request)
        131 
        132     replacement = self._prepare_response(self.response)


    ~/anaconda3/envs/PyOTA/lib/python3.6/site-packages/iota/commands/extended/send_trytes.py in _execute(self, request)
         43     gta_response = GetTransactionsToApproveCommand(self.adapter)(
         44       depth=depth,
    ---> 45       reference=reference,
         46     )
         47 


    ~/anaconda3/envs/PyOTA/lib/python3.6/site-packages/iota/commands/__init__.py in __call__(self, **kwargs)
        128       self.request = replacement
        129 
    --> 130     self.response = self._execute(self.request)
        131 
        132     replacement = self._prepare_response(self.response)


    ~/anaconda3/envs/PyOTA/lib/python3.6/site-packages/iota/commands/__init__.py in _execute(self, request)
        156     """
        157     request['command'] = self.command
    --> 158     return self.adapter.send_request(request)
        159 
        160   @abstract_method


    ~/anaconda3/envs/PyOTA/lib/python3.6/site-packages/iota/adapter/__init__.py in send_request(self, payload, **kwargs)
        302     )
        303 
    --> 304     return self._interpret_response(response, payload, {codes['ok']})
        305 
        306   def _send_http_request(self, url, payload, method='post', **kwargs):


    ~/anaconda3/envs/PyOTA/lib/python3.6/site-packages/iota/adapter/__init__.py in _interpret_response(self, response, payload, expected_status)
        443       context = {
        444         'request':  payload,
    --> 445         'response': decoded,
        446       },
        447     )


    BadApiResponse: 400 response from node: The subtangle is not solid



```python
transactionHash = []
for transaction in transfer["bundle"]:
    transactionHash.append(transaction.hash)
    print(transaction.address, transaction.hash)
```


    ---------------------------------------------------------------------------

    NameError                                 Traceback (most recent call last)

    <ipython-input-17-643384cfcf5b> in <module>()
          1 transactionHash = []
    ----> 2 for transaction in transfer["bundle"]:
          3     transactionHash.append(transaction.hash)
          4     print(transaction.address, transaction.hash)


    NameError: name 'transfer' is not defined


Let's check the statuses of our transactions. We need the corresponding hashes to be able to check if they were executed.


```python
api.get_latest_inclusion(transactionHash)
```


    ---------------------------------------------------------------------------

    ValueError                                Traceback (most recent call last)

    <ipython-input-18-7953d398015b> in <module>()
    ----> 1 api.get_latest_inclusion(transactionHash)
    

    ~/anaconda3/envs/PyOTA/lib/python3.6/site-packages/iota/api.py in get_latest_inclusion(self, hashes)
        635          }
        636     """
    --> 637     return extended.GetLatestInclusionCommand(self.adapter)(hashes=hashes)
        638 
        639   def get_new_addresses(


    ~/anaconda3/envs/PyOTA/lib/python3.6/site-packages/iota/commands/__init__.py in __call__(self, **kwargs)
        124     self.request = kwargs
        125 
    --> 126     replacement = self._prepare_request(self.request)
        127     if replacement is not None:
        128       self.request = replacement


    ~/anaconda3/envs/PyOTA/lib/python3.6/site-packages/iota/commands/__init__.py in _prepare_request(self, request)
        297       value           = request,
        298       filter_         = self.get_request_filter(),
    --> 299       failure_message = 'Request failed validation',
        300     )
        301 


    ~/anaconda3/envs/PyOTA/lib/python3.6/site-packages/iota/commands/__init__.py in _apply_filter(value, filter_, failure_message)
        332 
        333           context = {
    --> 334             'filter_errors': runner.get_errors(with_context=True),
        335           },
        336         )


    ValueError: Request failed validation ({'hashes': ['empty']}) (`exc.context["filter_errors"]` contains more information).


If the status is "True", it means that the transaction was executed and we could see that the tokens appeared on the receiver side.
But if we have only "False" that means that our proposed transaction wasn't validated and included in the tangle.
In this case, we could either wait or replay our request.


```python
api.replay_bundle(transactionHash[0], depth=depth) # should work on working and synced node
```


    ---------------------------------------------------------------------------

    IndexError                                Traceback (most recent call last)

    <ipython-input-19-910ceebc4a48> in <module>()
    ----> 1 api.replay_bundle(transactionHash[0], depth=depth) # should work on working and synced node
    

    IndexError: list index out of range


After successful inclusion, we should be able to see the transferred tokens in the receiver wallet:


```python
api = iota.Iota(uri, seed=sender_seed)
sender_account = api.get_account_data(start=0)
sender_account["balance"]
```




    0




```python
api = iota.Iota(uri, seed=receiver_seed)
receiver_account = api.get_account_data(start=0)
receiver_account["balance"]
```




    0



## Future enchancements:

### Check if address was used to spent: https://github.com/iotaledger/iota.lib.py/pull/158
Not pushed to release yet.

```
api = Iota(uri, seed=sender_seed)
r = api.were_addresses_spent_from(sender_address)
print(r)
```
