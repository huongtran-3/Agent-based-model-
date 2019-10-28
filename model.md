#GEOG5995 Assessment 1

#Model code:

import matplotlib
matplotlib.use('TkAgg')

import tkinter
import random
import operator
import matplotlib.pyplot
import matplotlib.animation 
import Agentframework
import requests
import bs4


num_of_agents = 10
num_of_iterations = 100
agents = []
neighbourhood = 20



fig = matplotlib.pyplot.figure(figsize=(7, 7))
ax = fig.add_axes([0, 0, 1, 1])
    
######################################################################
#######           Loading the environment         ##############
######################################################################
environment = []

import csv
f = open('data.txt', newline='')
reader = csv.reader(f, quoting=csv.QUOTE_NONNUMERIC)
for row in reader: # A list of rows
    rowlist = []
    for value in row:# A list of value
        rowlist.append(value)
    environment.append(rowlist)
#    print(value)
         # Floats
f.close()
######################################################################


######################################################################
#######           Getting coordinates from web         ##############
######################################################################
r = requests.get('http://www.geog.leeds.ac.uk/courses/computing/practicals/python/agent-framework/part9/data.html')
content = r.text
soup = bs4.BeautifulSoup(content, 'html.parser')
td_ys = soup.find_all(attrs={"class" : "y"})
td_xs = soup.find_all(attrs={"class" : "x"})
print(td_ys)
print(td_xs)
######################################################################



#ax.set_autoscale_on(False)

# Make the agents.
for i in range(num_of_agents):
     y = int(td_ys[i].text)
     x = int(td_xs[i].text)
     agents.append(Agentframework.Agent(environment, agents, y, x))

carry_on = True	
	
def update(frame_number):
    global carry_on
    
    fig.clear() 
    
    matplotlib.pyplot.xlim(0, 299)
    matplotlib.pyplot.ylim(0, 299)
    
    # plto the environemnt
    matplotlib.pyplot.imshow(environment)


    random.shuffle(agents)

    
    # Move the agents and eat and share.
    for i in range(num_of_agents):
        agents[i].move()
        agents[i].eat()
        agents[i].share_with_neighbours(neighbourhood)
      
    
    if random.random() < 0.01:
        carry_on = False
        print("random stopping condition")
    
    # plot agents on the environemnt
    for i in range(num_of_agents):
        matplotlib.pyplot.scatter(agents[i].get_x(),agents[i].get_y(), color = 'black')
		
def gen_function(b = [0]):
    a = 0
    global carry_on #Not actually needed as we're not assigning, but clearer
    while (a < 100) & (carry_on) :
        yield a			# Returns control and waits next call.
        a = a + 1





#animation = matplotlib.animation.FuncAnimation(fig, update, interval=1, repeat=False, frames=10)
#animation = matplotlib.animation.FuncAnimation(fig, update, frames=num_of_iterations, repeat=False)

root = tkinter.Tk()
root.wm_title("Model")
canvas = matplotlib.backends.backend_tkagg.FigureCanvasTkAgg(fig, master=root)
canvas._tkcanvas.pack(side=tkinter.TOP, fill=tkinter.BOTH, expand=1)

#animation

def run():
    animation = matplotlib.animation.FuncAnimation(fig, update, frames=gen_function, repeat=False)
    canvas.show()



# Just showing menu elements
menu_bar = tkinter.Menu(root)
root.config(menu=menu_bar)
model_menu = tkinter.Menu(menu_bar)
menu_bar.add_cascade(label="Model", menu=model_menu)
model_menu.add_command(label="Run model", command=run)
tkinter.mainloop() # Wait for interactions.
