---
layout: post
title:  "CRUD com React hooks e context API - #1 Reducer e State"
date:   2021-04-11 23:24:45 -0300
categories:  React Javascript 
---

Configurando nosso `GlobalState.js`

{% highlight jsx%}
//src>context>GlobalState.js
import React, {createContext, useReducer} from 'react'
import AppReducer from '/AppReducer'

//definind initial state
const initialState = {dummyUser:[
    {
        id: 1,
        name: 'dummyName1',
        email: 'email@dummy.com',
        role: 'dummyRole'
    }
]}

//creating context
export const GlobalContext = createContext(initialState)

//Provider component
export const GlobalProvider = ({children}) =>{
	const [state, dispatch] = useReducer(AppReducer, initialState)

	//CREATE, UPDATE and DELETE actions

    const createUser = user =>{
        dispatch({
            type: 'ADD_USER',
            payload: user
        })
    }

	const removeUser = id =>{
		dispatch({
			type: 'REMOVE_USER',
			payload: id
		})
	}

    const updateUser = user =>{
        dispatch({
            type: 'UPDATE_USER',
            payload: user
        })
    }

    return(
        <GlobalContext.Provider value = {
            {users: state.users,
            addUser,
            updateUSer}
        }>
        </GlobalContext.Provider>
    )
}

{% endhighlight %}<br/><bn/>

configurando nosso `AppReducer.js`

{% highlight jsx%}

const AppReducer = (state, action) =>{
    switch(action.type){
        case 'REMOVE_USER':
            return{
                users: state.users.filter(item=>item.id !== action.payload)
            }

        case 'ADD_USER':
            return{
                users: [action.payload, ...state.users]
            } 

        case 'UPDATE_USER':
            const updateUSers = state.users.map(item=>{
                if(item.id === action.payload.id){
                    return action.payload
                }
                return item
            }) 

            return{
                users: updateUsers
            }   

        default:
            return state       
    }
}

export default AppReducer

{% endhighlight %}<br/><bn/>