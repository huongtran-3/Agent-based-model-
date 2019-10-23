import random
class Agent():
    def __init__ (self, environment, agents, y, x):
        pass

        self._x=random.randint(0,299)
        self._y=random.randint(0,299)

        self.environment = environment
        self.agents = agents
        self.store = 0
        
        if (x == None):
            self._x = random.randint(0,299)
        else:
            self._x = x
            
        if (y == None):
            self._y = random.randint(0,299)
        else:
            self._y = y

    def move(self):
        if random.random() < 0.5:
            self._x = (self._x + 1) % 300
        else:
            self._x = (self._x - 1) % 300
        if random.random() < 0.5:
            self._y = (self._y + 1) % 300
        else:
            self._y = (self._y - 1) % 300

    def eat (self):
        if self.environment[self._y][self._x] > 10:
            self.environment[self._y][self._x] -= 10
            self.store += 10
    
    def set_x(self, x):
        self._x = x
        
    def get_x(self):
        return self._x
    
    def set_y (self, y):
        self._x = y
    
    def get_y(self):
        return self._y
    
    def share_with_neighbours(self, neighbourhood):
        for agent in self.agents:
            dist = self.distance_between(agent)
            if dist <= neighbourhood:
                sum = self.store + agent.store
                ave = sum /2
                self.store = ave
                agent.store = ave
                print("sharing " + str(dist) + " " + str(ave))
    
    def distance_between(self, agent):
        return (((self._x - agent._x)**2) + ((self._y - agent._y)**2))**0.5
