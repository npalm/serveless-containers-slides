<!-- .slide: data-background="images/future.jpg" data-transition="slide" data-background-transition="fade" -->


!SUB
<!-- .slide: data-background="images/ecs-on-ec2-network.png" data-transition="slide" data-background-transition="fade" -->

!SUB
<!-- .slide: data-background="images/ecs-on-fargate.png" data-transition="slide" data-background-transition="fade" -->

!SUB
# ECS terminology
- Task Definition - describes one or more containers
- Task - Instance of a task definition
- Service - Orchestrate tasks

!SUB
# Hands-on
- Network layer is already in place.
- We create:
  - A task definition
  - A load balancer
  - A service

!SUB
# [DEMO](https://us-east-1.console.aws.amazon.com/ecs/home?region=us-east-1#/clusters)

<!-- .slide: data-background-size="contain" data-background="images/giphy-programming.gif" data-transition="slide" data-background-transition="fade" -->

<div class="fragment"
<pre><code>
docker run -p 80:80 npalm/blog:latest
</pre></code></div>


!SLIDE
<!-- .slide: data-background="images/world.jpg" data-transition="slide" data-background-transition="fade" -->
<p class="source">
More Real World ;)</p><br><br><br><br><br>
<br><br><br><br>
[https://github.com/npalm/blog_terraform_aws_fargate](https://github.com/npalm/blog_terraform_aws_fargate)


!SUB
## Terraform
<img data-src="images/terraform-overview.png">

!SUB
## Network layer
<img data-src="images/ecs-diagram-network.png">


!SUB
## Load Balancer
<img data-src="images/ecs-diagram-lb.png">


!SUB
## Load Balancer Resources
```
resource "aws_alb" "main" {
  internal           = "false"
  subnets            = ["${module.vpc.public_subnets}"]
  ...
}

resource "aws_alb_listener" "main" {
  load_balancer_arn = "${aws_alb.main.id}"
  port              = "80"
  ...
}

resource "aws_alb_target_group" "main" {
  target_type = "ip"
  ...
}

```

!SUB
<img data-src="images/giphy-hackerman.gif" height="80%" width="80%">


!SUB
## Container (task) definition
<img data-src="images/ecs-diagram-task.png">



!SUB
## Container (task) definition
```
{
  "essential": true,
  "image": "npalm/blog:latest",
  "name": "blog",
  "portMappings": [
    {
      "hostPort": 80,
      "protocol": "tcp",
      "containerPort": 80
    }
  ],
  "logConfiguration": {
      ... log config ...
    }
  }
}
```

!SUB
## Task definition Resources
```
resource "aws_iam_role" "execution_role" { ... }

resource "aws_ecs_task_definition" "task" {
  container_definitions    = ... containter definition ...
  network_mode             = "awsvpc"
  cpu                      = "256"
  memory                   = "512"
  requires_compatibilities = ["FARGATE"]
  execution_role_arn       = "${aws_iam_role.execution_role.arn}"
}

```

!SUB
<img data-src="images/giphy-hackerman.gif" height="80%" width="80%">


!SUB
## Service
<img data-src="images/ecs-diagram-service.png">


!SUB
### Service Resources
```
resource "aws_security_group" "awsvpc_sg" {
  ... open port 80 ...
}

resource "aws_ecs_service" "service" {
  cluster         = "${aws_ecs_cluster.cluster.id}"
  task_definition = "${aws_ecs_task_definition.task.arn}"

  load_balancer = { ... connect to target group ... }
  launch_type   = "FARGATE"

  network_configuration { ... REQUIRES security_groups, subnets ... }
}
```

!SUB
<img data-src="images/giphy-hackerman.gif" height="80%" width="80%">

!SLIDE
# Fargate & EC2?

!SUB
<!-- .slide: data-background="images/ecs-mixed.png" data-transition="slide" data-background-transition="fade" -->

!SUB
## ECS - Fargate and EC2
<img data-src="images/ecs-fargate-2.png">
