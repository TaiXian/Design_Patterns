## Clean Code

#### NIC Practical Programmer Series

---

### Audience

- You are a programmer |
- You want to become a better programmer |
- Or ... |

---

### Moral Support

![Friends](assets/image/friends.jpg)

---

### Why is Clean Code Important

![Graph](assets/image/ProgrammerTimePieGrpah.JPG){ width=80%, height=80% }

>The ratio of time spent reading versus writing is well over 10 to 1. We are
>constantly reading old code as part of the effort to write new code

---

### Why is Clean Code Important

![Graph](assets/image/productivityTime.png)

---

### What is Clean Code to you?

![Comic](assets/image/cleanCodeComic.png){ width: 80%, height: 80% }

---

### Agenda

- Naming |
- Functions |
- Code Structure |
- Refactoring & Code Smells |

---

![Naming](assets/image/Naming.jpg)

###### Fun Fact: My firstborn male child will be called *Maximus*
---

### Naming

- Intention revealing names |
- Meaningful distinctions | 
- Searchable names |
- Avoid mental mapping |
- Nouns for classes and variables and verbs for functions |

---
#### Intention revealing names

```java
public List<int[]> getThem() {
  List<int[]> list1 = new ArrayList<int[]>();
  for (int[] x : theList)
    if (x[0] == 4)
      list1.add(x);
  return list1;
}
```

- What does this code do? |
- Why does it do it? |

+++

#### Compared to

```java
public List<Cell> getFlaggedCells() {
  List<Cell> flaggedCells = new ArrayList<Cell>();
  for (Cell cell : gameBoard)
    if (cell.isFlagged())
      flaggedCells.add(cell));
  return flaggedCells;
}
```

- How about now? |

---

#### Meaningful distinction

##### Function Names

```
getActiveAccount();
getActiveAccounts();
getActiveAccountInfo();
```

@[1-3](Which one would you use to get the Active Account?)

+++

##### Variable Names

```
moneyAmount vs money
customerInfo vs customer
theMessage vs message
accountData vs account
```

@[1-4](Don't make variables compete for attention)

---

#### Avoid Mental Mapping

```python
for i in range(34):
  s += (t[i]*4)/5;
```

@[2](Clearly everyone knows why we multiply times 4 and divide by 5)

+++ 

#### Searchable Names

```python
real_days_per_ideal_day = 4
work_days_per_week = 5
number_of_tasks = len(task_estimates)
sum = 0
for i in range(number_of_tasks):
  real_days = task_estimates[i] * real_days_per_ideal_day
  real_weeks = (real_days / work_days_per_week)
  sum += real_weeks
```
@[1-8](How about now?)
@[4](Although this name is great, it shadows a built in function)
@[3, 5](Why acces via array? Why not For In?)

---

#### Good naming can replace comments

```python
# Check to see if the employee is elegibile for bonus
if (employee.flag and employee.rating >= 4 && employee.years > 5)

if (employee.isEligibleForBonus)
```

---

### Naming

- Intention revealing names |
- Meaningful distinctions | 
- Searchable names |
- Avoid mental mapping |
- Nouns for classes and variables and verbs for functions |

---
### Functions

- Functons should be small, the smaller the better |
- A function should only do one thing |
- One level of abstraction |
- Less arguments are better |
- Impure Sandwhich -> No side effects* |

---

#### Building Blocks
##### Functions should be small

![Blocks](assets/image/blocks.jpeg)

---

#### Code Examples 

```java
public void Checkout()
{
  Price CurrentPrice = new Price();
  foreach(var product in CurrentShoppingCart)
    CurrentPrice.Add(product.Price);
  foreach(var coupon in CurrentDiscounts)
    CurrentPrice.Discount(coupon.Price);

  var billingInfo = BillingRepository.get(BillingId);
  if (billingInfo.CheckIfStillValid() == False)
  {
    DisplayError("Invalid Billing Information")
    return;
  }

  var paymentResult = PaymentGateway.Charge(billingInfo, CurrentPrice);
  if (paymentResult.Success == false)
  {
    DisplayError("Payment Processing Error");
    return;
  }
  DisplayMessage("Thank you for your order");
}
```
@[3-7](We first Calculate our Price by summing product prices and subtracting discount prices)
@[9-14](We then confirm if the billing info is valid)
@[16-23](Finally we charge the billingInfo card the currentPrice)
@[1-24](Think we can use our magic refactoring wand?)

+++

```java
public void Checkout()
{
  UpdateCurrentTotalPrice()
  ChargeCustomer()
  DisplayResult()
}

private void UpdateCurrentTotalPrice()
{
  Price CurrentPrice = new Price()
  SumShoppingCartProductsToCurrentPrice(CurrentPrice);
  ApplyShoppingCartDiscountsToCurrentPrice(CurrentPrice);
}

private void SumShoppingCartProductsToCurrentPrice()
{
  foreach(var product in CurrentShoppingCart)
    CurrentPrice.Add(product.Price);
}

// etc ...
```

@[1-6](High Level Abstraction)
@[8-13](Mid Level Abstraction)
@[15-19](Detail Level)

---

#### Impure Sandwich

![Sandwich](assets/image/cookie_sandwich.png)

+++

```python
# Simplified version of making a reservation at a restaurant
def makeReservation(quantity, date, restaurantId):
	restaurant = Restaurant.getRestaurant(restaurantId) # ORM 
	reservationsOnDate = filter(lambda reservation: reservation.date == date, restaurant.reservations)
	available_seats = restaurant.capacity - len(reservationsOnDate)
	if available_seats >= quantity:
		return Restaurant.reserve(data, quanitity) # Returns a reservation String from db call
	else:
		return None # Client Handles None
```

@[2-9](Code is easy to read, seems straighfoward what we are doing)
@[3](Here is our culprit, making we are mixing a database call with our business logic)
@[4-6](This is our business logic)
@[7](More of this impure **Hogwash**!)

+++

```python
# Impure Function Call
def getRestaurant(restaurantId):
	restaurant = Restaurant.getRestaurant(restaurant)


# Business Logic
@_.curry
def canMakeReservation(quantity, date, restaurant):
	reservationsOnDate = filter(lambda reservation: reservation.date == date, restaurant.reservations)
	available_seats = restaurant.capacity - len(reservationsOnDate)
	if available_seats >= quantity:
		return Some(Reservation(quantity, date)) # Creates a reservation object
	else:
		return None

#Impure Function Call
def reserve(reservation):
	return Restaurant.reserve(reservation)

# Yummy Impure Sandwhich
def makeReservation(quantity, date, restaurantId):
	return _.compose(
			getRestaurant(restaurantId),
			canMakeReservation(quantity, date),
			lambda reservation: reserve(reservation) if reservation else None # Does your language have Options?
		)
```
@[1-3](First Impure DB Call)
@[6-14](Business Logic Pure Function)
@[16-18](Second Impure DB Call)
@[20-26](We build our impure sandwhich via compose)
@[25](**Note** This Janky Ternary Operation, Does your language support Option?)

---

#### Let's play embarras the teacher by walking down Memory Lane

```python
# -*- coding: utf-8 -*-
"""
Created on Fri Dec 16 09:13:50 2016

@author: padien
"""


from pydash import _
import mu_fns
import mu_profile_logic
from mu_verification import entities_pass_verification
from mu_verification import profiles_pass_verification
from mu_verification import get_current_snapshot
from time import time, sleep
import datetime
import sys
import mu_rollback


def settings_upsert_from_csv(data, is_db_format, entity_type, comment, cs_api):
    def _create_formatted_proposed_update_list_by_tuid(_proposed_update_list):
        if not mu_fns.has_id(map_keys):
            return ''
            # found_objects = cs_api.post_entitiy_identifiers_for_search(proposed_update_list)
            # current_object_list = cs_fns.match_result_with_found_objects(proposed_update_list, found_objects)
        else:
            current_object_list = _.reduce_(_proposed_update_list, mu_fns.reduce_format_list_to_be_keyed_by_tuid, {})
        return current_object_list

    def _check_for_existing_proposed_entities(_proposed_entities_by_tuid, _entity_type):
        existing_proposed_entities = _.map_(_proposed_entities_by_tuid.keys(), lambda id: cs_api.get_proposed_entity(_entity_type, str(id)))
        has_proposed_entities = _.map_(existing_proposed_entities, lambda ent: _.get(ent, 'id', False))
        tuids_with_proposed = _.filter_(has_proposed_entities, lambda id: id is not False)
        return tuids_with_proposed

    def _reduce_rollback(acc, key):
        acc[key] = 1
        return acc

    def _verify(old_snap, new_snap, entity_type):
        if entity_type == 'profile':
            return profiles_pass_verification(formatted_csv_objects_by_tuid_with_profile, old_snap , new_snap)
        return entities_pass_verification(formatted_csv_objects_by_tuid_with_profile, old_snap , new_snap)

    def _reduce_exisiting_tuids(acc, value, id):
        if id in tuid_list:
            acc['exist'][id] = value
        else:
            acc['nonexistent'].append(id)
        return acc

    def reduce_get_current_ents(acc, id):
        try:
            result = cs_api.get_current_entity(entity_type, str(id))
            if result is not None:
                acc.append(result)
            return acc
        except:
            print ' failed to get ' + str(id)
            return acc

    def _reduce_update_and_submit_ents(acc, ent):
        try:
            cs_api.update_and_submit_entity(ent)
            return acc
        except:
            print {'failed_tuid': ent['id']}
            sys.stdout.flush()
            acc['failed'].append(ent['id'])
            return acc

    def _reduce_not_proposed_state(acc, value, key):
        if key not in existing_proposed_tuids:
            acc[key] = value
        return acc
        
    print {'started-1.2.2': datetime.datetime.fromtimestamp(time()).strftime('%Y-%m-%d %H:%M:%S')}
    sys.stdout.flush()
    message = {}
    # data comes in as array as rows of one string, we want rows of columns
    formatted_data = _.map_(data, lambda row: row.split(','))

    map_keys = mu_fns.create_mapping_from_header_row(formatted_data[0], is_db_format)
    csv_initial_object = {
        'valid' : {},
        'invalid_tuids' : [] #they are empty
    }
    print {'data':formatted_data}
    formatted_csv_objects_by_tuid = _.reduce_(formatted_data[1:], mu_fns.create_db_objects(map_keys), csv_initial_object)
    print {'formatted_csv_objects_by_tuid': formatted_csv_objects_by_tuid}

    existing_proposed_tuids = _check_for_existing_proposed_entities(formatted_csv_objects_by_tuid['valid'], entity_type)
    print {'existing_proposed': existing_proposed_tuids}
    if len(existing_proposed_tuids):
        message['existing_proposed'] = {'error': 'these entities are in draft state', 'ent_id': existing_proposed_tuids }
    valid_csv_objects_by_tuids = _.reduce(formatted_csv_objects_by_tuid['valid'], _reduce_not_proposed_state, {})

    #TODO: check for profiles existence
    current_entities_from_db = _.reduce_(valid_csv_objects_by_tuids.keys(), reduce_get_current_ents, [])
    #partition for tuids that dont exist
    tuid_list = _.map_(current_entities_from_db, lambda ent: _.get(ent, 'id', None))
    print {'tuid_list_length' : len(tuid_list)}
    partition = _.reduce_(valid_csv_objects_by_tuids, _reduce_exisiting_tuids, {'exist': {}, 'nonexistent': []})

    #check for profiles with break, get the data from current_ententities, and then merge it

    formatted_csv_objects_by_tuid_with_profile = mu_profile_logic._merge_profile_from_break(partition['exist'], current_entities_from_db, cs_api)
    # merge current_entities with proposal_entities
    initial_object = {
        'propose_objects' : [],
        'same_values_ids': []
    }
    entities_to_propose = _.reduce_(formatted_csv_objects_by_tuid_with_profile, mu_fns.create_proposed_entities(current_entities_from_db, comment), initial_object)
    print {'ents_to_propose': len(entities_to_propose['propose_objects'])}
    tuids_with_profile_violation_list =  _.map_(entities_to_propose['propose_objects'], lambda ent: mu_fns.check_profile_violation(ent))
    tuids_with_profile_violation_list_filtered = _.filter_(tuids_with_profile_violation_list, lambda tuid: tuid is not None)
    if len(tuids_with_profile_violation_list_filtered):
        message = _.merge(message, {'profile_error':
                                        {'message': 'These tuids have a profile and a setting in a category clashing',
                                         'tuids' : tuids_with_profile_violation_list_filtered}})

    entities_to_propose_filtered = _.filter_(entities_to_propose['propose_objects'], lambda ent: ent['id'] not in tuids_with_profile_violation_list_filtered)
    print {'proposed': len(entities_to_propose_filtered)}
    # have to check after upsert dif between old and new snapshot of the data
    print {'old_snap': datetime.datetime.fromtimestamp(time()).strftime('%Y-%m-%d %H:%M:%S')}
    sys.stdout.flush()
    old_snapshot =  get_current_snapshot(cs_api, entity_type) if (entity_type != 'profile') else get_current_snapshot(cs_api)
    result = _.reduce_(entities_to_propose_filtered, _reduce_update_and_submit_ents , {'failed': []})

    start = time()
    print {'sleeping': datetime.datetime.fromtimestamp(time()).strftime('%Y-%m-%d %H:%M:%S')}
    while (time() - start < 120): # necesary since we want to make sure the new_snapshot is valid
            sleep(1)
    print {'time_slept}': time() - start}
    print {'new_snap': datetime.datetime.fromtimestamp(time()).strftime('%Y-%m-%d %H:%M:%S')}
    sys.stdout.flush()
    new_snapshot = get_current_snapshot(cs_api, entity_type) if (entity_type != 'profile') else get_current_snapshot(cs_api)

    message['status'] = 200
    print 'verifying'
    sys.stdout.flush()
    verification = _verify(old_snapshot,new_snapshot, entity_type)
    #print {'verification' : verification}

    if not verification['passed_verification']:
        #needs to rollback all the entities
        entity_mapping = dict([('lowTouchTU', 'tu'),('highTouchTU', 'tu'), ('profile', 'profile')])
        entity_mapped = entity_mapping[entity_type]
        rollback_data = _.reduce_(verification['failed_verification'].keys(), _reduce_rollback, {})
        mu_rollback.mu_rollback(rollback_data,entity_mapped, 'Failed Verification', cs_api)
        return {'error':' TUIDS did not pass verification' , 'tuids': verification['failed_verification'].keys()}

    #N*M from failed so pretty small < 50, TODO:Optimize in CS
    failed_without_repetition = _.filter_(verification['tuids_failed_to_update'], lambda id: id not in entities_to_propose['same_values_ids'])

    message['ents_failed_to_update'] = failed_without_repetition
    message['report_counts'] = {
        'total_ents': len(formatted_data) - 1,
        'valid_total_ents': len(formatted_csv_objects_by_tuid['valid'].keys()),
        'expected_upsert': len(entities_to_propose_filtered),
        'existing_proposed_state': len(existing_proposed_tuids),
        'same_values_ids': len(entities_to_propose['same_values_ids']),
        'ent_does_not_exist': len(partition['nonexistent']),
        'actual_upsert': (len(entities_to_propose_filtered) - len(failed_without_repetition)),
        'empty': len(formatted_csv_objects_by_tuid['invalid_tuids']),
        'failed_to_update': len(failed_without_repetition)
    }
    message['empty_ents'] = formatted_csv_objects_by_tuid['invalid_tuids']
    message['same_values_ents'] = entities_to_propose['same_values_ids']
    message['ent_does_not_exist'] = partition['nonexistent']
    print {'finished': datetime.datetime.fromtimestamp(time()).strftime('%Y-%m-%d %H:%M:%S')}
    sys.stdout.flush()
    return message
```

---

### Functions

- Functons should be small, the smaller the better |
- A function should only do one thing |
- One level of abstraction |
- Less arguments are better |
- Impure Sandwhich -> No side effects* |


