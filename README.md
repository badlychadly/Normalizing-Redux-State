# Normalizing-Redux-State

### What is a Normalized State??
A Normalized State can be thought of as a way of structuring the Redux state to handle nested data without causing rerenders in ancestor components. Whenever dealing with deeply nested relational data (such as a has many, many to many, or belongs to) we should consider normalizing our state, because nested data causes more complex logic in our reducer when trying to update or make changes on a nested field. Think of a normalized state as a representation of a database in that each type of data should get its own 'table'.


### Becoming Normal
The basic ToDo model is a good example of some relational data. Where a List has many items, and an Item belongs to a list. 
```
#List Object
{
    id: '1',
    title: 'listTitle',
    items: [
      {
        id: 'item1',
        name: 'itemname1'
      },
      {
        id: 'item2',
        name: 'itemname2'
      }
    ]
  }
```
You can see this list object has an array of item objects. With a state structured like this altering one item requires us to find the list in state then find the item in that lists array plus the logic to split that array apart to rejoin an item object with the changes or however one decides to do it. Trying to scale this code with more deeply nested data will get ugly very fast.

Now take a look at this normalized version:
```
{
    lists : {
        byId : {
            "list1" : {
                id : "list1",
                title: "list1Title"
                items : ["item1", "item2"]
            },
            "list2" : {
                id : "list2",
                title: "list2Title"
                items : ["item3", "item4", "item5"]
            }
        },
        allIds : ["list1", "list2"]
    },
    items : {
        byId : {
            "item1" : {
                id : "item1",
                name: "item1name"
            },
            "item2" : {
                id : "item2",
                name: "item2name"
            },
            "item3" : {
                id : "item3",
                name: "item3name"
            },
            "item4" : {
                id : "item4",
                name: "item4name"
            },
            "item5" : {
                id : "item5",
                name: "item5name"
            },
        },
        allIds : ["item1", "item2", "item3", "item4", "item5"]
    }
}
```
At first glance this can be a little intimidating but let's break this down. We now have an object with 2 keys for each type of data `lists:` and `items:`. In either of those objects there are two more keys byId and allIds. The byId object is just storing the lists or items objects with a key that is equal to the id of the object. This makes it much easier for finding a list because instead of iterating through each list we can just use the id of the list we want `state.lists[list1]`. The allIds array is just storing all of data type ids. The other thing to note is that in a list object instead of storing an array of item objects we now just store an array of the associated itemIds. These can be passed on to children props and then used to grab that item from the items object.

Now to update an item we just need the itemId and the value we are changing. 
```
#reducer

return {
  ...state,
	  items: { 
		  ...state.items,
			[action.itemId]: {
			  ...state.items[action.itemId],
				name: action.value
			}
		}
}
```
You can break this logic up more by using the Redux `combineReducers()` function. The result of normalizing Redux state can help the UI only update what has changed, and should always be considered for nested and relational data.

#### Get your redux fill and practice from the official Redux site! 
[Normalizing State](https://redux.js.org/recipes/structuring-reducers/normalizing-state-shape)
