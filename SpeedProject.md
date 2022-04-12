# Speed Project


## Objective

- To build an asynchronous web app with Draggable, and droppable cards.



# Design

- ![GIF](speedgif2.gif)



## Technologies

- ASP.NET
- Blazor 
- SignalR
- C#



## Execution

To accomplish the key features of this game We used the SignalR library for asynchronous communication between browser instances, and Blazor components for Draggable and droppable cards.

## Code snippets of my work

Blazor Component code


` @if (IsPlayerOne)
        {
            
            //PlayerOne (current player) play and pull piles on the left
            <div class="col-2 px-0">
                <HiddenCard ClickEvent="@(async () => {await PullCards();})" />
            </div>
            <div class="col-2 px-0">
                <PlayPile  PlayPile_1="Table.PlayOne"
                           Card="Table.PlayOne.Peek()"                           
                           DraggedCard="DraggedCard" 
                           MoveActiveCardEvent="(() => MoveActiveCard(DraggedCard, playPile1))"
                           DragStartEvent="(() => HandleDragStart(Table.PlayOne.Peek()))"/>
            </div>
            <div class="col-2 px-0">
                <PlayPile  PlayPile_1="Table.PlayTwo"
                           Card="Table.PlayTwo.Peek()"
                           DraggedCard="DraggedCard" 
                           MoveActiveCardEvent="(() => MoveActiveCard(DraggedCard, playPile2))"
                           DragStartEvent="(() => HandleDragStart(Table.PlayTwo.Peek()))"/>
            </div>
            <div class="col-2 px-0">
                <HiddenCard ClickEvent="@(async () => {await PullCards();})" />
            </div>
        }
`

SignalR Hub code

` public async Task PlayCard(Table table, Card card, string playerGuid, bool pileOne) 
        {
            int playerNum = GetPlayerNumber(playerGuid);

            if (playerNum == 1 && pileOne)
            {
                table.PlayerOneHand.Remove(card);
                table.PlayOne.Push(card);
            }
            else if (playerNum == 1 && !pileOne)
            {
                table.PlayerOneHand.Remove(card);
                table.PlayTwo.Push(card);
            }
            else if (playerNum == 2 && pileOne)
            {
                table.PlayerTwoHand.Remove(card);
                table.PlayOne.Push(card);
            }
            else if (playerNum == 2 && !pileOne)
            {
                table.PlayerTwoHand.Remove(card);
                table.PlayTwo.Push(card);
            }

            await SendTable(table);
        }

        public async Task PullCard(Table table)
        {
            if (table.PullOne.Count < 1 || table.PullTwo.Count < 1)
            {
                table = ShufflePlayAndPull(table);
            }
            else
            {
                table.PlayOne.Push(table.PullOne.Pop());
                table.PlayTwo.Push(table.PullTwo.Pop());
            }

            await SendTable(table);
        }

        public Table ShufflePlayAndPull(Table table)
        {
            while (table.PlayOne.Count > 0)
            {
                table.Discard.Add(table.PlayOne.Pop());
            }

            while (table.PlayTwo.Count > 0)
            {
                table.Discard.Add(table.PlayTwo.Pop());
            }

            while (table.PullOne.Count > 0)
            {
                table.Discard.Add(table.PullOne.Pop());
            }

            while (table.PullTwo.Count > 0)
            {
                table.Discard.Add(table.PullTwo.Pop());
            }

            table.Shuffle(table.Discard);

            table.PlayOne.Push(table.Discard[0]);
            table.PlayTwo.Push(table.Discard[1]);

            for (int i = 2; i < 8; i++)
            {
                table.PullOne.Push(table.Discard[i]);
            }
            for (int i = 8; i < 12; i++)
            {
                table.PullTwo.Push(table.Discard[i]);
            }

            table.Discard.Clear();

            return table;
        }
`

### Download
- [Speed Game](https://github.com/Chase-CK/Speed/archive/refs/heads/master.zip)
