userservice.java

package com.example.jobportal.service;

import com.example.jobportal.dto.UserDTO;
import com.example.jobportal.exception.CustomException;
import com.example.jobportal.model.User;
import com.example.jobportal.repository.UserRepository;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.stereotype.Service;

import java.util.Map;

@Service
public class UserService {

    private final UserRepository userRepository;
    private final PasswordEncoder passwordEncoder;

    public UserService(UserRepository userRepository, PasswordEncoder passwordEncoder) {
        this.userRepository = userRepository;
        this.passwordEncoder = passwordEncoder;
    }

    private UserDTO toDto(User u) {
        UserDTO dto = new UserDTO();
        dto.setId(u.getId());
        dto.setName(u.getName());
        dto.setEmail(u.getEmail());
        dto.setRole(u.getRole().name());
        return dto;
    }

    public Map<String, Object> register(String name, String email, String password, String role) {
        userRepository.findByEmail(email).ifPresent(x -> {
            throw new CustomException("Email already in use", 400);
        });
        User u = new User();
        u.setName(name);
        u.setEmail(email);
        u.setPassword(passwordEncoder.encode(password));
        try {
            u.setRole(User.Role.valueOf(role.toUpperCase()));
        } catch (IllegalArgumentException ex) {
            throw new CustomException("Invalid role. Use SEEKER/EMPLOYER/ADMIN", 400);
        }
        u = userRepository.save(u);
        return Map.of("message", "User registered successfully", "user", toDto(u));
    }

    public Map<String, Object> login(String email, String password) {
        User u = userRepository.findByEmail(email)
                .orElseThrow(() -> new CustomException("Invalid credentials", 401));
        if (!passwordEncoder.matches(password, u.getPassword())) {
            throw new CustomException("Invalid credentials", 401);
        }
        // TODO: integrate JwtUtil to generate a real token later
        return Map.of("token", "mock-jwt-token", "user", toDto(u));
    }

    public UserDTO getProfile(Long userId) {
        User u = userRepository.findById(userId)
                .orElseThrow(() -> new CustomException("User not found", 404));
        return toDto(u);
    }

    public UserDTO updateProfile(Long userId, UserDTO update) {
        User u = userRepository.findById(userId)
                .orElseThrow(() -> new CustomException("User not found", 404));
        if (update.getName() != null) u.setName(update.getName());
        if (update.getEmail() != null) u.setEmail(update.getEmail());
        if (update.getRole() != null) u.setRole(User.Role.valueOf(update.getRole().toUpperCase()));
        u = userRepository.save(u);
        return toDto(u);
    }
}







jobservice.java

package com.example.jobportal.service;

import com.example.jobportal.dto.JobDTO;
import com.example.jobportal.exception.CustomException;
import com.example.jobportal.model.Job;
import com.example.jobportal.model.User;
import com.example.jobportal.repository.JobRepository;
import com.example.jobportal.repository.UserRepository;
import org.springframework.stereotype.Service;

import java.util.List;
import java.util.stream.Collectors;

@Service
public class JobService {

    private final JobRepository jobRepository;
    private final UserRepository userRepository;

    public JobService(JobRepository jobRepository, UserRepository userRepository) {
        this.jobRepository = jobRepository;
        this.userRepository = userRepository;
    }

    private JobDTO toDto(Job j) {
        JobDTO dto = new JobDTO();
        dto.setId(j.getId());
        dto.setTitle(j.getTitle());
        dto.setDescription(j.getDescription());
        dto.setRequirements(j.getRequirements());
        dto.setSalaryRange(j.getSalaryRange());
        dto.setEmployerId(j.getEmployer() != null ? j.getEmployer().getId() : null);
        return dto;
    }

    private void apply(Job j, JobDTO dto) {
        j.setTitle(dto.getTitle());
        j.setDescription(dto.getDescription());
        j.setRequirements(dto.getRequirements());
        j.setSalaryRange(dto.getSalaryRange());
        if (dto.getEmployerId() != null) {
            User employer = userRepository.findById(dto.getEmployerId())
                    .orElseThrow(() -> new CustomException("Employer not found", 404));
            j.setEmployer(employer);
        }
    }

    public JobDTO createJob(JobDTO dto) {
        Job j = new Job();
        apply(j, dto);
        j = jobRepository.save(j);
        return toDto(j);
    }

    public JobDTO updateJob(Long id, JobDTO dto) {
        Job j = jobRepository.findById(id)
                .orElseThrow(() -> new CustomException("Job not found", 404));
        apply(j, dto);
        j = jobRepository.save(j);
        return toDto(j);
    }

    public List<JobDTO> listJobs() {
        return jobRepository.findAll().stream().map(this::toDto).collect(Collectors.toList());
    }

    public void deleteJob(Long id) {
        if (!jobRepository.existsById(id)) throw new CustomException("Job not found", 404);
        jobRepository.deleteById(id);
    }

    public List<JobDTO> searchJobs(String keyword) {
        String kw = keyword == null ? "" : keyword.toLowerCase();
        return jobRepository.findAll().stream()
                .filter(j -> (j.getTitle() != null && j.getTitle().toLowerCase().contains(kw)) ||
                             (j.getDescription() != null && j.getDescription().toLowerCase().contains(kw)) ||
                             (j.getRequirements() != null && j.getRequirements().toLowerCase().contains(kw)))
                .map(this::toDto)
                .collect(Collectors.toList());
    }
}




applicationservicejava

package com.example.jobportal.service;

import com.example.jobportal.dto.ApplicationDTO;
import com.example.jobportal.exception.CustomException;
import com.example.jobportal.model.Application;
import com.example.jobportal.model.Job;
import com.example.jobportal.model.User;
import com.example.jobportal.repository.ApplicationRepository;
import com.example.jobportal.repository.JobRepository;
import com.example.jobportal.repository.UserRepository;
import org.springframework.stereotype.Service;

import java.util.List;
import java.util.stream.Collectors;

@Service
public class ApplicationService {

    private final ApplicationRepository applicationRepository;
    private final JobRepository jobRepository;
    private final UserRepository userRepository;

    public ApplicationService(ApplicationRepository applicationRepository, JobRepository jobRepository, UserRepository userRepository) {
        this.applicationRepository = applicationRepository;
        this.jobRepository = jobRepository;
        this.userRepository = userRepository;
    }

    private ApplicationDTO toDto(Application a) {
        ApplicationDTO dto = new ApplicationDTO();
        dto.setId(a.getId());
        dto.setJobId(a.getJob() != null ? a.getJob().getId() : null);
        dto.setSeekerId(a.getSeeker() != null ? a.getSeeker().getId() : null);
        dto.setStatus(a.getStatus().name());
        return dto;
    }

    public ApplicationDTO applyJob(ApplicationDTO dto) {
        Job job = jobRepository.findById(dto.getJobId())
                .orElseThrow(() -> new CustomException("Job not found", 404));
        User seeker = userRepository.findById(dto.getSeekerId())
                .orElseThrow(() -> new CustomException("Seeker not found", 404));
        Application a = new Application();
        a.setJob(job);
        a.setSeeker(seeker);
        a.setStatus(Application.Status.APPLIED);
        a = applicationRepository.save(a);
        return toDto(a);
    }

    public List<ApplicationDTO> viewApplicationsForSeeker(Long seekerId) {
        return applicationRepository.findAll().stream()
                .filter(a -> a.getSeeker() != null && a.getSeeker().getId().equals(seekerId))
                .map(this::toDto)
                .collect(Collectors.toList());
    }

    public List<ApplicationDTO> viewApplicationsForEmployer(Long employerId) {
        return applicationRepository.findAll().stream()
                .filter(a -> a.getJob() != null &&
                             a.getJob().getEmployer() != null &&
                             a.getJob().getEmployer().getId().equals(employerId))
                .map(this::toDto)
                .collect(Collectors.toList());
    }

    public ApplicationDTO updateStatus(Long id, String status) {
        Application a = applicationRepository.findById(id)
                .orElseThrow(() -> new CustomException("Application not found", 404));
        try {
            a.setStatus(Application.Status.valueOf(status.toUpperCase()));
        } catch (IllegalArgumentException ex) {
            throw new CustomException("Invalid status. Use APPLIED/REVIEWED/ACCEPTED/REJECTED", 400);
        }
        a = applicationRepository.save(a);
        return toDto(a);
    }
}


resumeservice.java

package com.example.jobportal.service;

import com.example.jobportal.dto.ResumeDTO;
import com.example.jobportal.exception.CustomException;
import com.example.jobportal.model.Resume;
import com.example.jobportal.model.User;
import com.example.jobportal.repository.ResumeRepository;
import com.example.jobportal.repository.UserRepository;
import org.springframework.stereotype.Service;

import java.time.Instant;
import java.util.List;
import java.util.stream.Collectors;

@Service
public class ResumeService {

    private final ResumeRepository resumeRepository;
    private final UserRepository userRepository;

    public ResumeService(ResumeRepository resumeRepository, UserRepository userRepository) {
        this.resumeRepository = resumeRepository;
        this.userRepository = userRepository;
    }

    private ResumeDTO toDto(Resume r) {
        ResumeDTO dto = new ResumeDTO();
        dto.setId(r.getId());
        dto.setSeekerId(r.getSeeker() != null ? r.getSeeker().getId() : null);
        dto.setFileUrl(r.getFileUrl());
        dto.setUploadedAt(r.getUploadedAt());
        return dto;
    }

    public ResumeDTO uploadResume(ResumeDTO dto) {
        User seeker = userRepository.findById(dto.getSeekerId())
                .orElseThrow(() -> new CustomException("Seeker not found", 404));
        Resume r = new Resume();
        r.setSeeker(seeker);
        r.setFileUrl(dto.getFileUrl());
        r.setUploadedAt(Instant.now());
        r = resumeRepository.save(r);
        return toDto(r);
    }

    public List<ResumeDTO> listResumes(Long seekerId) {
        return resumeRepository.findAll().stream()
                .filter(r -> r.getSeeker() != null && r.getSeeker().getId().equals(seekerId))
                .map(this::toDto)
                .collect(Collectors.toList());
    }

    public void deleteResume(Long id) {
        if (!resumeRepository.existsById(id)) throw new CustomException("Resume not found", 404);
        resumeRepository.deleteById(id);
    }
}


